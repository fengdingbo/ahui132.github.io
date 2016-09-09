# Future
Future 是抽象的Task, Task is subclass of Future

1. Encapsulates(包括) the asynchronous execution of a callable.
2. Future instances are created by Executor.submit() and should not be created directly except for testing.
3. Future could wrap coroutine as task, and store it's return value

Future is Not thread safe!

## Future Objects Method
Future instances are created by future = asyncio.Future()


    future.cancel()
    Attempt to cancel the call. If the call is currently being executed then it cannot be cancelled and the method will return False,

    future.cancelled()
    Return True if the call was successfully cancelled.

    future.running()
    Return True if the call is currently being executed and cannot be cancelled.

    future.done()
    Return True if the call was successfully cancelled or finished running.

    future.result(timeout=None)
    Return the value returned by the call. If the call hasn’t yet completed then this method will wait up to timeout seconds.

# Coroutine Category

    coroutine asyncio.sleep(delay, result=None, *, loop=None)
        Create a coroutine that completes after a given time (in seconds).
        The resolution of the sleep depends on the granularity of the event loop.
