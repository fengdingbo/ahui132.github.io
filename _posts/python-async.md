---
layout: page
title:
category: blog
description:
---
# Preface

# 非阻塞IO
在IO操作的过程中，当前线程被挂起，而其他需要CPU执行的代码就无法被当前线程执行了。

1. 多线程和多进程的模型虽然解决了并发问题，但切换线程的开销也很大，一旦线程数量过多，CPU的时间就花在线程切换上了
2. 另一种解决IO问题的方法是异步IO。它只发出IO指令，并不等待IO结果，然后就去执行其他代码了。一段时间后，当IO返回结果时，再通知CPU进行处理。

异步IO模型需要一个消息循环，在消息循环中，主线程不断地重复“读取消息-处理消息”这一过程：

	loop = get_event_loop()
	while True:
		event = loop.get_event()
		process_event(event)

消息模型是如何解决同步IO必须等待IO操作这一问题的呢？当遇到IO操作时，代码只负责发出IO请求，不等待IO结果，然后直接结束本轮消息处理，进入下一轮消息处理过程。当IO操作完成后，将收到一条“IO完成”的消息，处理该消息时就可以直接获取IO操作结果。

# TODO
https://docs.python.org/3/library/asyncio-task.html#asyncio.iscoroutinefunction

## BaseEventLoop
https://docs.python.org/3/library/asyncio-eventloop.html#asyncio-hello-world-callback

# coroutine
coroutine 可通过generator 实现(没有线程那样的上下文切换开销, 也不需要锁机制, 多进程+多协程可以获得非常好的性能)

传统的生产者-消费者模型是一个线程写消息，一个线程取消息，通过锁机制控制队列和等待，但一不小心就可能死锁。

如果改用协程，生产者生产消息后，直接通过yield跳转到消费者开始执行，待消费者执行完毕后，切换回生产者继续生产，效率极高：

	def consumer():
		r = 'init'
		while True:
			n = yield r
			if not n:
				return
			print('[CONSUMER] Consuming %s...' % n)
			r = '200 OK'

	def produce(c):
		print(c.send(None));# init
		n = 0
		while n < 5:
			n = n + 1
			print('[PRODUCER] Producing %s...' % n)
			r = c.send(n)
			print('[PRODUCER] Consumer return: %s' % r)
		c.close()

	c = consumer()
	produce(c)

执行结果：

	[PRODUCER] Producing 1...
	[CONSUMER] Consuming 1...
	[PRODUCER] Consumer return: 200 OK
	[PRODUCER] Producing 2...
	[CONSUMER] Consuming 2...
	[PRODUCER] Consumer return: 200 OK
	[PRODUCER] Producing 3...
	[CONSUMER] Consuming 3...
	[PRODUCER] Consumer return: 200 OK
	....

注意到consumer函数是一个generator.

整个流程无锁，由一个线程执行，produce和consumer协作完成任务，所以称为“协程”，而非线程的抢占式多任务。

> Donald Knuth的一句话总结协程的特点： “子程序就是协程的一种特例。”

# asyncio
asyncio的编程模型就是一个消息循环。我们从asyncio模块中直接获取一个EventLoop的引用，然后把需要执行的*协程扔到EventLoop*中执行，就实现了异步IO。

## yield from subcoroutine
*yield from subcoroutine* 用于将自己放入到EventLoop 队列, 等待*subcoroutine* 执行

用asyncio 实现Hello world 代码如下(同一线程)：

	import threading
	import asyncio

	@asyncio.coroutine
	def hello():
		print('Hello world! (%s)' % threading.currentThread())
		yield from asyncio.sleep(1)
		print('Hello again! (%s)' % threading.currentThread())

	loop = asyncio.get_event_loop()
	# loop.run_until_complete(hello())
	loop.run_until_complete(asyncio.wait([hello(), hello()]))
	loop.close()

观察执行过程：

	Hello world! (<_MainThread(MainThread, started 140735195337472)>)
	Hello world! (<_MainThread(MainThread, started 140735195337472)>)
	(暂停约1秒)
	Hello again! (<_MainThread(MainThread, started 140735195337472)>)
	Hello again! (<_MainThread(MainThread, started 140735195337472)>)

由打印的当前线程名称可以看出，两个coroutine是由同一个线程并发执行的。

1. `@asyncio.coroutine`把一个`generator`标记为`coroutine-future`类型，
2. 然后，我们就把这个`coroutine`扔到EventLoop中执行。
3. `yield from`语法暂停并调用另一个`sub-coroutione`(coroutine 内不可再使用`yield generator`)
4. 由于asyncio.sleep()也是一个coroutine，它也会暂停，而是直接中断并执行下一个消息循环。当asyncio.sleep()返回时，线程就可以从yield from拿到返回值（此处是None），然后接着执行下一行语句。相当于把本task 放到eventLoop

把asyncio.sleep(1)看成是一个耗时1秒的IO操作，在此期间，主线程并未等待，而是去执行EventLoop中其他可以执行的coroutine了，因此可以实现并发执行。

### yield from reader.readline()
我们用asyncio的异步网络连接来获取sina、sohu和163的网站首页：

	import asyncio

	@asyncio.coroutine
	def wget(host):
		print('wget %s...' % host)
		connect = asyncio.open_connection(host, 80)
		reader, writer = yield from connect
		header = 'GET / HTTP/1.0\r\nHost: %s\r\n\r\n' % host
		writer.write(header.encode('utf-8'))
		yield from writer.drain()
		while True:
			line = yield from reader.readline()
			if line == b'\r\n':
				break
			print('%s header > %s' % (host, line.decode('utf-8').rstrip()))
		# Ignore the body, close the socket
		writer.close()

	loop = asyncio.get_event_loop()
	tasks = [wget(host) for host in ['www.sina.com.cn', 'www.sohu.com', 'www.163.com']]
	loop.run_until_complete(asyncio.wait(tasks))
	loop.close()

执行结果如下：

	wget www.sohu.com...
	wget www.sina.com.cn...
	wget www.163.com...
	(等待一段时间)
	(打印出sohu的header)
	www.sohu.com header > HTTP/1.1 200 OK
	www.sohu.com header > Content-Type: text/html
	...
	(打印出sina的header)
	www.sina.com.cn header > HTTP/1.1 200 OK
	www.sina.com.cn header > Date: Wed, 20 May 2015 04:56:33 GMT
	...
	(打印出163的header)
	www.163.com header > HTTP/1.0 302 Moved Temporarily
	www.163.com header > Server: Cdn Cache Server V2.0
	...

可见3个连接由一个线程通过coroutine并发完成。

## async/await
用asyncio 提供的`@asyncio.coroutine` 可以把一个generator标记为coroutine类型，然后在coroutine内部用yield from调用另一个coroutine实现异步操作。

为了简化并更好地标识异步IO，从Python 3.5开始引入了新的语法async和await，可以让coroutine的代码更简洁易读。

请注意，async和await是针对coroutine的新语法，要使用新的语法，只需要做两步简单的替换：

1. 把`@asyncio.coroutine`替换为`async`: 将generator 变成 coroutine；
2. 把`yield from`替换为`await`: 后面接一个子例程 sub coroutine, 会被加入到`asyncio.get_event_loop`，
	loop 会循环侦测状态并执行(没有await 的coroutine 不会被执行)
	`a = await func()` 其实是异步的`yield func()`

让我们对比一下上一节的代码：

	@asyncio.coroutine
	def hello():
		print("Hello world!")
		r = yield from asyncio.sleep(1)
		print("Hello again!")

用新语法重新编写如下：

	async def hello():
		print("Hello world!")
		r = await asyncio.sleep(1)
		print("Hello again!")

> Python从3.5版本开始为asyncio提供了async和await的新语法；

## async-with
首先异步获取响应，然后异步读取响应

	import asyncio
	from aiohttp import ClientSession
	async def hello():
	    async with ClientSession() as session:
	        async with session.get("http://baidu.com") as response:
	            response = await response.read()
	            print(response)
	loop = asyncio.get_event_loop()
	loop.run_until_complete(hello())

ClientSession允许在多个请求之间保存cookie以及相关对象信息。
Session(会话)在使用完毕之后需要关闭，关闭Session是另一个异步操作，所以每次你都需要使用async with关键字

### multi url

	import asyncio
	from aiohttp import ClientSession
	async def fetch(url):
	    async with ClientSession() as session:
	        async with session.get(url) as response:
	            return await response.read()

	async def run(loop,  r):
	    url = "http://baidu.com/{}"
	    tasks = []
	    for i in range(r):
	        task = asyncio.ensure_future(fetch(url.format(i)))
	        tasks.append(task)
	    responses = await asyncio.gather(*tasks)
	    # you now have all response bodies in this variable: because of await
	    print(responses)

	loop = asyncio.get_event_loop()
	future = asyncio.ensure_future(run(loop, 4))
	loop.run_until_complete(future)

官方的例子

	import asyncio
	async def coro(name, lock):
	    print('coro {}: waiting for lock'.format(name))
	    async with lock:
	        print('coro {}: holding the lock'.format(name))
	        await asyncio.sleep(1)
	        print('coro {}: releasing the lock'.format(name))

	loop = asyncio.get_event_loop()
	lock = asyncio.Lock()
	coros = asyncio.gather(coro(1, lock), coro(2, lock))
	try:
	    loop.run_until_complete(coros)
	finally:
	    loop.close()

will output:

	coro 2: waiting for lock
	coro 2: holding the lock
	coro 1: waiting for lock
	coro 2: releasing the lock
	coro 1: holding the lock
	coro 1: releasing the lock

Note that both `async for` and `async with` can only be used *inside a coroutine* function declared with `async def`.

# asyncio-task

## coroutine

### coroutine define
Coroutines used with asyncio may be implemented using:

0. Generator
1. Generator-based coroutines: should be decorated with @asyncio.coroutine, and use *yield from*
2. async def

- define: iscoroutinefunction(define)
- instance: iscoroutine(instance)

### coroutine can do
1. await future(yield from):
  suspends the coroutine until the future is done(or raise excetion)
2. await coroutine:
  wait for another coroutine to produce a result (or raise an exception)
3. return expression: return an expression to *await coroutine*
3. return exception: raise an exception to *await coroutine*

### coroutine run
coroutine does not run until you call:

1. *await coroutine* from another coroutine(already running)
2. *schedule* its execution using the `ensure_future()` function or the `AbstractEventLoop.create_task()` method

## Task
http://stackoverflow.com/questions/34753401/difference-between-coroutine-and-future-task-in-python-3-5

Schedule *the execution of a coroutine*: wrap it in a future. A task is a subclass of Future.

	# vim /usr/local/Cellar/python3/3.5.1/Frameworks/Python.framework/Versions/3.5/lib/python3.5/asyncio/tasks.py
	# vim /usr/local/Cellar/python3/3.5.1/Frameworks/Python.framework/Versions/3.5/lib/python3.5/asyncio/futures.py
	class Task(futures.Future):

This class *asyncio.Task(coro, *, loop=None)* is not thread safe(like future), and has more methods than Future

	classmethod all_tasks(loop=None)
		Return a set of all tasks for an event loop or current loop.
	classmethod current_task(loop=None)
		Return the currently running task in an event loop or None.
	cancel()
		Request that this task cancel itself.
	get_stack(*, limit=None)
		Return the list of stack frames for this task’s coroutine.
			stack of suspended: is not done:
			empty:	completed/cancelled
			traceback frames: terminated by an exception
	print_stack(*, limit=None, file=None)
		Print the stack or traceback for this task’s coroutine.

### create task
Don’t directly create Task instances, pls use:

1. asyncio.ensure_future(coroutine_or_future) function
2. or the AbstractEventLoop.create_task() method.

	//创建一个任务
	future = asyncio.Future()
	asyncio.ensure_future(slow_operation(future))

ensure_future will wrap *loop.create_task(coro_or_future)* in tasks.py

	def ensure_future(coro_or_future, *, loop=None):
	    if isinstance(coro_or_future, futures.Future):
	        return coro_or_future
	    elif coroutines.iscoroutine(coro_or_future):
	        if loop is None:
	            loop = events.get_event_loop()
	        task = loop.create_task(coro_or_future);  task._loop == loop
	        return task

#### Example: future vs task
http://stackoverflow.com/questions/34753401/difference-between-coroutine-and-future-task-in-python-3-5

	import asyncio
	async def slow_operation():
	    await asyncio.sleep(1)
	    return 'Future is done!'

	def got_result(future):
	    print(future.result())
	    # We have result, so let's stop
	    loop.stop()

	loop = asyncio.get_event_loop()
	task = loop.create_task(slow_operation())
	task.add_done_callback(got_result)

	# We run forever
	loop.run_forever()

use future

	import asyncio

	@asyncio.coroutine
	def slow_operation(future):
	    yield from asyncio.sleep(1)
	    future.set_result('Future is done!')

	def got_result(future):
	    print(future.result())
	    loop.stop()

	loop = asyncio.get_event_loop()
	future = asyncio.Future()
	asyncio.ensure_future(slow_operation(future))
	future.add_done_callback(got_result)
	try:
	    loop.run_forever()
	finally:
	    loop.close()

#### Example: Parallel execution of tasks
Example executing 3 tasks (A, B, C) in parallel:

	import asyncio

	@asyncio.coroutine
	def factorial(name, number):
	    f = 1
	    for i in range(2, number+1):
	        print("Task %s: Compute factorial(%s)..." % (name, i))
	        yield from asyncio.sleep(1)
	        f *= i
	    print("Task %s: factorial(%s) = %s" % (name, number, f))

	loop = asyncio.get_event_loop()
	tasks = [
	    asyncio.ensure_future(factorial("A", 2)),
	    asyncio.ensure_future(factorial("B", 3)),
	    asyncio.ensure_future(factorial("C", 4))]
	print("start..");
	loop.run_until_complete(asyncio.gather(*tasks))
	loop.close()

Output:

	start...
	Task A: Compute factorial(2)...
	Task B: Compute factorial(2)...
	Task C: Compute factorial(2)...
	Task A: factorial(2) = 2

## gather
1. 将tasks 包装成 futures(如果没有)
2. futures 各自set_result

    outer = _GatheringFuture(children, loop=loop)
    for i, fut in enumerate(children):
        fut.add_done_callback(functools.partial(_done_callback, i)); # set result
    return outer

# aiohttp
> 如果把asyncio用在服务器端，例如Web服务器，由于HTTP连接就是IO操作，因此可以用单线程+coroutine实现多用户的高并发支持。
> asyncio实现了TCP、UDP、SSL等协议，aiohttp则是基于asyncio实现的HTTP框架。

我们先安装aiohttp：

	pip3 install aiohttp

然后编写一个HTTP服务器，分别处理以下URL：

	/ - 首页返回b'<h1>Index</h1>'；
	/hello/{name} - 根据URL参数返回文本hello, %s!。

代码如下：

	import asyncio
	from aiohttp import web

	async def index(request):
	    await asyncio.sleep(0.5)
	    return web.Response(body=b'<h1>Index</h1>')

	async def hello(request):
	    await asyncio.sleep(0.5)
	    text = '<h1>hello, %s!</h1>' % request.match_info['name']
	    return web.Response(body=text.encode('utf-8'))

	async def init(loop):
	    app = web.Application(loop=loop)
	    app.router.add_route('GET', '/', index)
	    app.router.add_route('GET', '/hello/{name}', hello)
	    srv = await loop.create_server(app.make_handler(), '127.0.0.1', 8000)
	    print('Server started at http://127.0.0.1:8000...')
	    return srv

	loop = asyncio.get_event_loop()
	loop.run_until_complete(init(loop))
	loop.run_forever(); # init completed, run server forever

注意aiohttp的初始化函数init()也是一个coroutine，`loop.create_server()` 则利用asyncio创建TCP服务。

> https://docs.python.org/3/library/asyncio-eventloop.html

- run_until_complete

	vim /usr/local/Cellar/python3/3.5.1/Frameworks/Python.framework/Versions/3.5/lib/python3.5/asyncio/base_events.py +291

## aiohttp.Timeout

	async def test():
		with aiohttp.Timeout(10):
			do sth...

# EventLoop

	loop = asyncio.get_event_loop()

## run_until_complete

	loop.run_until_complete(coroutine)
	loop.run_until_complete(asyncio.wait([coroutine1, coroutine2]))

## asyncio.wait

    $ vim /usr/local/Cellar/python3/3.5.1/Frameworks/Python.framework/Versions/3.5/lib/python3.5/asyncio/tasks.py +313
    Usage:
        done, pending = yield from asyncio.wait(fs)

    done, pending = set(), set()
    for f in fs:
        f.remove_done_callback(_on_completion)
        if f.done():
            done.add(f)
        else:
            pending.add(f)
    return done, pending

## create_server

	from aiohttp import web
    app = web.Application(loop=loop)
    app.router.add_route('GET', '/hello/{name}', hello)
    srv = await loop.create_server(app.make_handler(), '127.0.0.1', 8000); # create TCP task

# send-await
> 只有types.coroutine 却没有定义 yield from 的, 则还是generator, 但是可以应用于`await generator`, 这个是个黑盒子..
send 与`await` 与 `yield`类似, 会`step into coroutine`. 但是.. 不能再次coroutine.send(None)

	import types
	import time
	import asyncio
	async def switch():
        print('switch')
        a=(await asyncio.sleep(1))
        print("return %d", a);
        a=(await asyncio.sleep(1))
        print("return %d", a);

	async def coro1():
        print("C1: Start")
        await switch()
        print("C1: Stop")

	async def coro2():
        print("C2: Start")
        print("C2: a")
        print("C2: b")
        await switch()
        print("C2: c")
        print("C2: Stop")

	c1 = coro1()
	c2 = coro2()
	def run(coros):
        coros = list(coros)

        while coros:
            print(coros)
            print('start loop')
            time.sleep(1)
            # Duplicate list for iteration so we can remove from original list.
            for coro in list(coros):
                try:
                    print(coro.send(None))
                    #c1 = coro
                except StopIteration:
                    coros.remove(coro)
	run([c1,c2])

# Other

## Watch a file descriptor for read events
Wait until a file descriptor received some data using the `BaseEventLoop.add_reader()` method and then close the event loop:

	import asyncio
	try:
	    from socket import socketpair
	except ImportError:
	    from asyncio.windows_utils import socketpair

	# Create a pair of connected file descriptors
	rsock, wsock = socketpair()
	loop = asyncio.get_event_loop()

	def reader():
	    data = rsock.recv(100)
	    print("Received:", data.decode())
	    # We are done: unregister the file descriptor
	    loop.remove_reader(rsock)
	    # Stop the event loop
	    loop.stop()

	# Register the file descriptor for read event
	loop.add_reader(rsock, reader)

	# Simulate the reception of data from the network
	loop.call_soon(wsock.send, 'abc'.encode())

	# Run the event loop
	loop.run_forever()

	# We are done, close sockets and the event loop
	rsock.close()
	wsock.close()
	loop.close()

## Set signal handlers for SIGINT and SIGTERM
Register handlers for signals SIGINT and SIGTERM using the `BaseEventLoop.add_signal_handler()` method:

	import asyncio
	import functools
	import os
	import signal

	def ask_exit(signame):
		print("got signal %s: exit" % signame)
		loop.stop()

	loop = asyncio.get_event_loop()
	for signame in ('SIGINT', 'SIGTERM'):
		loop.add_signal_handler(getattr(signal, signame),
								functools.partial(ask_exit, signame))

	print("Event loop running forever, press Ctrl+C to interrupt.")
	print("pid %s: send SIGINT or SIGTERM to exit." % os.getpid())
	try:
		loop.run_forever()
	finally:
		loop.close()
