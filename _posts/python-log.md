# Preface

# logging
logging，和assert比，logging不会抛出错误，而且可以输出到文件：

	import logging
	logging.basicConfig(filename='example.log',level=logging.DEBUG)

	logging.info('n = %d' % n)
    # 捕捉异常并使用 traceback 记录它
    logger.exception(msg, _args)，它等价于 logger.error(msg, exc_info=True, _args)。

logging 允许你指定记录信息的级别，有debug，info，warning，error等几个级别，当我们指定level=INFO时，logging.debug就不起作用了。

	logging.basicConfig(level=logging.INFO)

logging的另一个好处是通过简单的配置，一条语句可以同时输出到不同的地方，比如console和文件。

术语

    Logger 记录器，暴露了应用程序代码能直接使用的接口。
    Handler 处理器，将（记录器产生的）日志记录发送至合适的目的地。
    Filter 过滤器，提供了更好的粒度控制，它可以决定输出哪些日志记录。
    Formatter 格式化器，指明了最终输出中日志记录的布局。

## logger 记录器
文／好吃的野菜（简书作者）
原文链接：http://www.jianshu.com/p/feb86c06c4f4
著作权归作者所有，转载请联系作者获得授权，并标注“简书作者”。

1. logger是一个树形层级结构，在使用接口debug，info，warn，error，critical之前必须创建Logger实例，即创建一个记录器，
2. 如果没有显式的进行创建，则默认创建一个*root logger*，并应用默认的日志级别(WARN)，
3. 处理器Handler(*StreamHandler*，即将日志信息打印输出在标准输出上)，和格式化器Formatter(默认的格式即为第一个简单使用程序中输出的格式)。

    logger = logging.getLogger(__name__)

然后可:

    logger.setLevel(logging.ERROR) # 设置日志级别为ERROR，即只有日志级别大于等于ERROR的日志才会输出
    logger.addHandler(handler_name) # 为Logger实例增加一个处理器
    logger.removeHandler(handler_name) # 为Logger实例删除一个处理器

## handler 处理
比较常用的有三个，StreamHandler，FileHandler，NullHandler

    sh = logging.StreamHandler(stream=None)
    fh = logging.FileHandler(filename, mode='a', encoding=None, delay=False)
    rh = logging.RotatingFileHandler;   #文件的大小会随着时间推移而不断增大, 使用旋转文件句柄替代 FileHandler。
    nh = loggine.NullHandler(); # 本质上它是个“什么都不做”的handler，由库开发者使用。

创建Handler之后，可以通过使用以下方法设置日志级别，设置格式化器Formatter，增加或删除过滤器Filter。

    ch.setLevel(logging.WARN) # 指定日志级别，低于WARN级别的日志将被忽略
    ch.setFormatter(formatter_name) # 设置一个格式化器formatter
    ch.addFilter(filter_name) # 增加一个过滤器，可以增加多个
    ch.removeFilter(filter_name) # 删除一个过滤器

## Formatter 格式化器
使用Formatter对象设置日志信息最后的规则、结构和内容，默认的时间格式为%Y-%m-%d %H:%M:%S。

    formatter = logging.Formatter(fmt=None, datefmt=None)

其中，fmt是消息的格式化字符串，datefmt是日期字符串。如果不指明fmt，将使用'%(message)s'。如果不指明datefmt，将使用ISO8601日期格式。

## Filter 过滤器
Handlers和Loggers 可以使用Filters来完成比级别更复杂的过滤。

1. Filter基类只允许特定Logger层次以下的事件。例如用‘A.B’初始化的Filter允许Logger ‘A.B’, ‘A.B.C’, ‘A.B.C.D’, ‘A.B.D’等记录的事件，logger‘A.BB’, ‘B.A.B’ 等就不行。
2. 如果用空字符串来初始化，所有的事件都接受。

    filter = logging.Filter(name='')

## 层次
Logger是一个树形层级结构;

1. Logger可以包含一个或多个Handler和Filter，即Logger与Handler或Fitler是一对多的关系;
2. 一个Logger实例可以新增多个Handler，一个Handler可以新增多个格式化器或多个过滤器，而且日志级别将会继承。

![python-log-1.png](/img/python-log-1.png)

### logger flow
1. 判断日志的等级是否大于Logger对象的等级，如果大于，则往下执行，否则，流程结束。
2. 产生日志。
第一步，判断是否有异常，如果有，则添加异常信息。第二步，处理日志记录方法(如debug，info等)中的占位符，即一般的字符串格式化处理。
3. 使用注册到*Logger对象中的Filters*进行过滤。如果有多个过滤器，则依次过滤；只要有一个过滤器返回假，则过滤结束，且该日志信息将丢弃，不再处理，而处理流程也至此结束。否则，处理流程往下执行。
4. 搜索注册到*Logger对象中查找Handlers*，如果找不到任何Handler，则往上到该Logger对象的父Logger中查找；如果找到一个或多个Handler，则依次用Handler来处理日志信息。但在每个Handler处理日志信息过程中，会首先判断日志信息的等级是否大于该Handler的等级，如果大于，则往下执行(由Logger对象进入Handler对象中)，否则，处理流程结束。
5. 执行注册到*Handler对象中的filter*方法，该方法会依次执行注册到该Handler对象中的Filter。如果有一个Filter判断该日志信息为假，则此后的所有Filter都不再执行，而直接将该日志信息丢弃，处理流程结束。
5. 使用*Formatter类*格式化最终的输出结果。 注：Formatter同上述第2步的字符串格式化不同，它会添加额外的信息，比如日志产生的时间，产生日志的源代码所在的源文件的路径等等。
7. 真正地输出日志信息(到网络，文件，终端，邮件等)。至于输出到哪个目的地，由Handler的种类来决定。

## 配置方式

    显式创建记录器Logger、处理器Handler和格式化器Formatter，并进行相关设置；
    通过简单方式进行配置，使用basicConfig()函数直接进行配置；
    通过配置文件进行配置，使用fileConfig()函数读取配置文件；
    通过配置字典进行配置，使用dictConfig()函数读取配置信息；
    通过网络进行配置，使用listen()函数进行网络配置。

### 显式配置
使用程序logger.py如下:

    import logging

    # create logger
    logger_name = "example"
    logger = logging.getLogger(logger_name)
    logger.setLevel(logging.DEBUG)

    # create file handler
    log_path = "./log.log"
    fh = logging.FileHandler(log_path)
    fh.setLevel(logging.WARN)

    # create formatter
    fmt = "%(asctime)-15s %(levelname)s %(filename)s %(lineno)d %(process)d %(message)s"
    datefmt = "%a %d %b %Y %H:%M:%S"
    formatter = logging.Formatter(fmt, datefmt)

    # add handler and formatter to logger
    fh.setFormatter(formatter)
    logger.addHandler(fh)

### basicConfig关键字参数

    关键字	描述
    filename	创建一个FileHandler，使用指定的文件名，而不是使用StreamHandler。
    filemode	如果指明了文件名，指明打开文件的模式（如果没有指明filemode，默认为'a'）。
    format	handler使用指明的格式化字符串。
    datefmt	使用指明的日期／时间格式。
    level	指明根logger的级别。
    stream	使用指明的流来初始化StreamHandler。该参数与'filename'不兼容，如果两个都有，'stream'被忽略。

有用的format格式

    %(levelno)s	%(levelname)s	%(pathname)s	%(filename)s	%(funcName)s	%(lineno)d	%(asctime)s	%(thread)d	%(threadName)s	%(process)d	%(message)s

### 文件配置
配置文件logging.conf如下:

    [loggers]
    keys=root,example,monitor

    [logger_root]
    level=DEBUG
    handlers=hand01,hand02

    # logger = logging.getLogger('example')
    [logger_example]
    handlers=hand01,hand02
    qualname=example
    propagate=0

    [handlers]
    keys=hand01,hand02

    [handler_hand01]
    class=StreamHandler
    level=INFO
    formatter=form02
    args=(sys.stderr,)

    [handler_hand02]
    class=FileHandler
    # class=handlers.RotatingFileHandler
    level=DEBUG
    formatter=form01
    args=('log.log', 'a')

    [formatters]
    keys=form01,form02

    [formatter_form01]
    format=%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s

use:

    logging.config.fileConfig("./logging.conf")
    logger = logging.getLogger("example")

### 字典配置

	logging.config.dictConfig(json.load())
	logging.config.dictConfig(yaml.load())
	or
	LOG_CFG=my_logging.json python my_server.py
	LOG_CFG=my_logging.yaml python my_server.py

### 监听配置
    loggging.config.listen(port=DEFAULT_LOGGING_CONFIG_PORT)


### log with color

#### coloredlogs
http://stackoverflow.com/a/384125/2140757

	import coloredlogs, logging
	coloredlogs.install(level='DEBUG')
	logging.info("It works!")
	logging.getLogger(__name__).debug("work")

or

	logging.addLevelName( logging.WARNING, "\033[1;31m%s\033[1;0m" % logging.getLevelName(logging.WARNING))
	logging.addLevelName( logging.ERROR, "\033[1;41m%s\033[1;0m" % logging.getLevelName(logging.ERROR))


#### origin log with color

	import logging
	logger = logging.getLogger(__name__)

	log_format = '[%(levelname)s] %(asctime)s \033[1;31m %(filename)s +%(lineno)d \033[1;0m: %(message)s'
	date_fmt = '%a, %d %b %Y %H:%M:%S'
	formatter = logging.Formatter(log_format, date_fmt)

	ch = logging.StreamHandler()
	ch.setFormatter(formatter)
	logger.addHandler(ch)
	logger.setLevel(logging.DEBUG)
	logger.debug('ttttxx')

# Reference
文／好吃的野菜（简书作者）
原文链接：http://www.jianshu.com/p/feb86c06c4f4
著作权归作者所有，转载请联系作者获得授权，并标注“简书作者”。
