# instpect

    print(__file__)
    print(module.__file__)

inspect

    import inspect

    def PrintFrame():
      callerframerecord = inspect.stack()[1]    # 0 represents this line
                                                # 1 represents line at caller
      frame = callerframerecord[0]
      info = inspect.getframeinfo(frame)
      print info.filename                       # __FILE__     -> Test.py
      print info.function                       # __FUNCTION__ -> Main
      print info.lineno                         # __LINE__     -> 13

    def Main():
      PrintFrame()                              # for this line

or:

    import inspect
    frame = inspect.currentframe()
    # __FILE__
    frame.f_code.co_filename
    frame.f_lineno

## inspect class

    import inspect
    inspect.getfile(C.__class__)

# Directory

## file property

### file exists

	from os.path import exists
	exists(file)

### isfile, isdir

    os.path.isfile(path)
    os.path.isdir(path)

### file.stat

	import os
	st = os.stat(path)
    if st.st_uid == euid:
        return st.st_mode & stat.S_IRUSR != 0

### access permision
Use the real uid/gid to test for access to a path.

	access(path, mode, *, dir_fd=None, effective_ids=False, follow_symlinks=True)
		mode
			Operating-system mode bitfield.  Can be F_OK to test existence,
			or the inclusive-OR of R_OK, W_OK, and X_OK.

example:

	if os.access(file, os.R_OK):
		# Do something
	else:
		if not os.access(file, os.R_OK):
			print(file, "is not readable")
		else:
			print("Something went wrong with file/dir", file)
		break

#### chmod

	os.chmod(path, mode)
	os.chown(path, uid, gid)

## mkdir/rename/delete/

	os.mkdir(dir,mode=511)
		os.makedirs(newDir/dir);//recursive
	os.rmdir('/Users/michael/testdir')
        import shutil
        shutil.rmtree('/path/to/your/dir/'); # recursive

### rename file:

	os.rename(fileA, existedDir/fileB)
	os.move(fileA, existedDir/fileB)
		os.renames('dirA', 'newDir/DirB')//auto create dir
		os.renames('a', 'c/')// rename a to c (rmdir c if c is existed)

> `shutil.move()` uses `os.rename()` if the destination is on the current filesystem.
Otherwise, `shutil.move()` copies the source to destination using `shutil.copy2()` and then removes the source.

### copy file
shutil模块提供了copyfile()的函数，你还可以在shutil模块中找到很多实用函数，它们可以看做是os模块的补充。

`copy2` is also often useful, it preserves the `original modification and access info (mtime and atime)` in the file metadata.

	shutil.copyfile(src, existedDir/dst)
	shutil.copy2(src, existedDir/dst)

### remove file

	os.rmdir(empty_dir);	//empty dir
		shutil.rmtree(dir_only)
	os.remove(file_only);	//file_only

## mkfile fifo

	os.mkfifo(path, mode=438, *, dir_fd=None)
        Create a "fifo" (a POSIX named pipe).

## tmpfile

    fp = tempfile.TemporaryFile();
    fp.close()

    > tempfile.NamedTemporaryFile().name
    /var/folders/73/7vxr7kzs09ndh3kwzw9zpdj80000gn/T/tmpykjnhjnj
    with tempfile.TemporaryDirectory() as tmpdirname:

    > tmpdir = tempfile.mkdtemp(); # string

## listdir

### via os.listdir

	>>> [x for x in os.listdir('.') if os.path.isfile(x) and os.path.splitext(x)[1]=='.py']
	['apis.py', 'config.py', 'models.py', 'pymonitor.py', 'test_db.py', 'urls.py', 'wsgiapp.py']

### via glob.glob
Starting with Python version 3.5, the glob module supports the `"**"` directive for recursive
(which is parsed only if you pass `recursive flag`):

	import glob
	glob.glob(pathname, *, recursive=False); # list
	glob.iglob(pathname, *, recursive=False); # iterator

	for filename in glob.iglob('src/**/*.c', recursive=True):
		print(filename)

If you need an list, just use `glob.glob` instead of `glob.iglob`.

## pathinfo

### dirname:

	path = os.path.dirname(amodule.__file__)

### abspath:

	os.path.abspath('.')
	os.path.abspath(amodule.__file__)

在某个目录下创建一个新目录，首先把新目录的完整路径表示出来:

	>>> os.path.join('/Users/michael', 'testdir')
	'/Users/michael/testdir'

### split pathinfo

	>>> os.path.split('/Users/michael/testdir/file.txt')
	('/Users/michael/testdir', 'file.txt')

	>>> os.path.splitext('/path/to/file.txt')
	('/path/to/file', '.txt')

### current directory

	os.getcwd()
	os.chdir(path)
	os.chroot(path)

# File

	print open('a.php').read(); //cat a.php

## IOError

	import glob, os
	try:
		for file in glob.glob("\\*.txt"):
			with open(file) as fp:
				# do something with file
	except IOError:
		print("could not read", file)


## Open File

	fp = open(filename[, mode])
	mode:
		'r' 'w' 'a'
		append a '+' to the mode to allow simultaneous reading and writing
			'r+' read and write
			'w+' empty old content and write
			'a+' read and append
		append a 'U' to the mode open a file for input with universal new line support('\r', '\n', '\r\n')
			 'U' cannot be combined with 'w' or '+' mode.
	fp.close()

### stdin file
参考  python-io.md

	import fileinput
	for line in fileinput.input():
		print(line)

### binary

	>>> f = open('/Users/michael/test.jpg', 'rb')
	>>> f.read()
	b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节

### file encoding

	open('a.txt').encoding

用gbk 打开且忽略非法编码

	>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk', errors='ignore')

## read and write
> pydoc file

	fp.read()	Return file content.
	fp.readline()	Return just one line of text file.
	fp.write(string)	Writes string to file(start at file position)
	fp.writeline(string)	Writes string to file(start at file position)
	fp.truncate(...)
		truncate([size]) -> None.  Truncate the file to at most size bytes.
		Size defaults to the current file position, as returned by tell().

Example:

	fp = open('a.txt')
	print fp.read()
	fp.close();

## close file
由于文件读写时都有可能产生IOError，一旦出错，后面的f.close()就不会调用。
所以，为了保证无论是否出错都能正确地关闭文件，我们可以使用try ... finally来实现：

	try:
		f = open('/path/to/file', 'r')
		print(f.read())
	finally:
		if f:
			f.close()

但是每次都这么写实在太繁琐，所以，Python引入了with语句来自动帮我们调用close()方法：

	with open('/path/to/file', 'r') as f:
		print(f.read())

## file position
`pydoc file`

	fp.tell(...)
		tell() -> current file position, an integer (may be a long integer).
	.seek(...)
		.seek(offset[, whence]) -> None.  Move to new file position.
		Optional argument whence defaults to:
			0 (offset from start of file, offset should be >= 0);
			1 (move relative to current position, positive or negative),
			2 (move relative to end of file, usually negative, although many platforms allow seeking beyond the end of a file).
			If the file is opened in text mode, only offsets returned by tell() are legal.  Use of other offsets causes undefined behavior.

### line position

	function seek_line(f, line){
		f.seek(0);
		while(line-- >0){
			f.getline();
		}
	}
