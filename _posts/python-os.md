---
layout: page
title:
category: blog
description:
---
# Preface

# platform

	import platform
	system = platform.system()
	if system == 'Darwin':  # 如果是Mac OS X


# os

	>>> import os
	>>> os.name # 操作系统类型
	'posix'

要获取详细的系统信息，可以调用uname()函数：

	>>> os.uname()
	posix.uname_result(sysname='Darwin', nodename='MichaelMacPro.local', release='14.3.0', version='Darwin Kernel Version 14.3.0: Mon Mar 23 11:59:05 PDT 2015; root:xnu-2782.20.48~5/RELEASE_X86_64', machine='x86_64')

## cpu

	import threading, multiprocessing

	multiprocessing.cpu_count()

# sys

## MEMORY, getsizeof
If you need to know MEMORY USAGE of a given type, you can use the function sys.getsizeof

	>>> from sys import getsizeof
	>>> l = []
	>>> getsizeof(l)
	64
	>>> getsizeof("toto")
	53
	>>> getsizeof(10.5)
	24

# User

	import os, stat

    uid = os.getuid()
    euid = os.geteuid()
    gid = os.getgid()
    egid = os.getegid()

# 环境变量
在操作系统中定义的环境变量，全部保存在os.environ这个变量中，可以直接查看：

	>>> os.environ
	environ({'VERSIONER_PYTHON_PREFER_32_BIT': 'no', 'TERM_PROGRAM_VERSION': '326', 'LOGNAME': 'michael', 'USER': 'michael', 'PATH': '/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/opt/X11/bin:/usr/local/mysql/bin', ...})
	要获取某个环境变量的值，可以调用os.environ.get('key')：

	>>> os.environ.get('PATH')
	'/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/opt/X11/bin:/usr/local/mysql/bin'
	>>> os.environ.get('x', 'default')
	'default'

	os.environ.get('HOME')
