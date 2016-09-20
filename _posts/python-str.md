## Bytes

	>>> type(b'abc')
	<class 'bytes'>
	>>> type('abc')
	<class 'str'>

	>>> type(b'abc'[2])
	<class 'int'>


# String
double quotes and single quotes is same

	print "a\nb" ;# The character here "\n" is new line
	print 'a\nb'
	print '\x30\x31'; 01
	print '\xb8'; b'\xc2\xb8' 其实是按utf8 解释

## format

	'hello, {name}'.format(name='Wang')
	'hello, {0}-{1}'.format('Wang', 'Kang')

## encoding

### unicode
以下三个等价, 且都是 class str

	u'\u4e2d'
	u'中'
	'中'

### str to bytes
默认是*class str* 是binary. 可以通过encode 将*class str*为字节*class bytes*

class bytes: ascii 不变, 其它则用16进制表示

	'abc'.encode('ascii')
		b'abc'
	'中国'.encode('utf8')
		b'\xe4\xb8\xad'
	'中国'.encode('GB2312')
		b'\xd6\xd0'

	b'\xd6\xd0'.decode('GB2312')
		中

## len

	len('中'); 2
	len('中'.encode('utf8')); 6
	len('ab'); 2
	len('ab'.encode('utf8')); 2

## ord/chr

	ord('A'); 65
	chr(65); 'A'

## Access String
like list

	'hilojack'[4:]
	'hilo' in 'hilojack'

## string func

	## substring count
	'hilo hilo'.count('hilo')

	## capitalize
	'hilo'.capitalize()
	'hilo'.upper()
	'hilo'.lower()

	## len
	len('abc')

	## split
	'ab cd'.split(' ') # ['ab', 'cd']

	## sorted
	sorted('a zx') # [' ', 'a', 'x', 'z']

## search replace

	'hilojack'.find('jack');//4
	'hilo' in 'hilojack'
	str.replace(needle, word, 1); //replace the first needle with word

### find

    str.find(substr, beg=0, end=len(string))
        Index if found and -1 otherwise.

## pad
zfill

	$ print '1'.zfill(2);
	01

## trim
包括\n, ' ', '\t\r'

	'a\n  '.strip() + ',end'

## Concat String

	>>> print 'a'+'b'+'c'	# with no space
	abc	# "abc\n"
	>>> print 'a' 'b' 	'c'   # with no space
	>>> print 'a''b''c'   # with no space
	abc # "abc\n"
	>>> print var1 var2   # syntax error
	>>> print '-'*6
	------
	>>> print '-' * 6
	------

long delimiter `"""` and `'''`(same): `\n` is still transfered by python

	print """
	a\nbc
	"""
	print """ab\nc"""
	print '''ab\nc'''

### Escape Sequences

	\n	ASCII linefeed (LF)
	\N{name}	Character named name in the Unicode database (Unicode only)
	\r ASCII	Carriage Return (CR)
	\uxxxx	Character with 16-bit hex value xxxx (Unicode only)
	\Uxxxxxxxx	Character with 32-bit hex value xxxxxxxx (Unicode only)
	\v	ASCII vertical tab (VT)
	\ooo	Character with octal value ooo
	\xhh	Character with hex value hh

#### not Escape
use repr encode

	>>> string = "abc\ndef"
	>>> print (repr(string))
	>>> 'abc\ndef'

	>>> print(repr("\n"))
	'\n'
	>>> print(r"\n")
    \n

### print

	>>> print 'a', 'b', 'c' # with space and new line
	a b c	# "a b c\n"
	>>> print 'a', 'b', 'c',# with space only
	a b c	# "a b c "

with no space and new line:

	>> sys.stdout.write('string')
	string

## Format String
%s

	>>> format='%s'
	>>> print format % 'part1' 'part2'
	part1part2

%r

	>>> print 'This is (%r) (%s)' % ("Hilojack\"", "Blog")
	This is ('Hilojack"') (Blog)
	>>> print '''This is (%r) (%s)''' % ("Hilojack\"", "Blog")
	This is ('Hilojack"') (Blog)

> %r displays the "raw" data

## string like list

	print "abc"[-1]
	for in "abc"

## hex

	hex(16)

## json

	import json
	str = json.dumps(data)
	print(json.loads(str))
    >>> json.dumps(a, ensure_ascii=False).encode('utf-8')
    b'{"a": "\xe4\xb8\xad\xe5\x9b\xbd\xe4\xba\xbahahha"}'
    >>> json.dumps(a, ensure_ascii=True).encode('utf-8')
    b'{"a": "\\u4e2d\\u56fd\\u4ebahahha"}'

### json class
json dumps 类时，得用default

	class Student(object):
		def __init__(self, name, age, score):
			self.name = name
			self.age = age
			self.score = score

	s = Student('Bob', 20, 88)
	>>> print(json.dumps(s, default=lambda m: return { 'name': std.name, 'age': std.age, 'score': std.score }))
	{"age": 20, "name": "Bob", "score": 88}

反序列化为class

	def dict2student(d):
		return Student(d['name'], d['age'], d['score'])
	运行结果如下：

	>>> json_str = '{"age": 20, "score": 88, "name": "Bob"}'
	>>> print(json.loads(json_str, object_hook=dict2student))

## chunk

	def chunkstring(string, length):
	    return (string[0+i:length+i] for i in range(0, len(string), length))

	import re
	def chunkstring(string, length):
		return re.findall('\.{1,'+length+'}',string)
