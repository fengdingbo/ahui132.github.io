---
layout: page
title:
category: blog
description:
---
# Preface


# Test
assert False, "Error!"

# Yield

	def X(): yield Y; X().next()

# Http
[python-http](/p/python-http)

# string
/p/python-str

# mysql

## MySQLdb

	import MySQLdb as mysql

	db = mysql.connect(user="reboot",passwd="reboot123",db="memory",host="localhost")
	db.autocommit(True)
	cur = db.cursor()

    sql = 'insert into memory (memory,time) value (%s,%s)'%(1024,1234)
    cur.execute(sql)
