# doctest

## multiline

http://wiki.python.org/moin/MultiLineStringsInDocTest

use command continuation characters in input for multiline, use `<BLANKLINE>` in output for blank line

`<BLANKLINE>` is a doctest keyword

	>>> mline = '''foo
	...
	... bar'''
	>>> print mline
	foo
	<BLANKLINE>	
	bar

## unicode in doctest

http://stackoverflow.com/questions/1733414/how-do-i-include-unicode-strings-in-python-doctests

- already described hack with sys.setdefaultencoding("UTF-8")
- unicode docstring (already mentioned above too, thanks a lot)
- AND print statement.

## run doctest

	python -mdoctest -v example.py


不要定义默认参数为空array, 见下面例子

	def foo(x=[]):
		'''
		>>> foo()
		[1]
		>>> foo()
		[1, 1]
		'''
		x.append(1)
		return x


PEP 366 -- Main module explicit relative imports

	if __name__ == "__main__" and __package__ is None:
		__package__ = "expected.package.name"

http://stackoverflow.com/questions/8299270/ultimate-answer-to-relative-python-imports


5
down vote
Take a look at the following info from PEP 328:

Relative imports use a module's __name__ attribute to determine that module's position in the package hierarchy. If the module's name does not contain any package information (e.g. it is set to '__main__') then relative imports are resolved as if the module were a top level module, regardless of where the module is actually located on the file system.

When you run foo.py as a script, that module's __name__ is '__main__', so you cannot do relative imports. This would be true even if mypackage was on sys.path. Basically, you can only do relative imports from a module if that module was imported.

Here are a couple of options for working around this:

1) In foo.py, check if __name__ == '__main__' and conditionally add mypackage to sys.path:

	if __name__ == '__main__':
		import os, sys
		# get an absolute path to the directory that contains mypackage
		foo_dir = os.path.dirname(os.path.join(os.getcwd(), __file__))
		sys.path.append(os.path.normpath(os.path.join(foo_dir, '..', '..')))
		from mypackage import bar
	else:
		from .. import bar
2) Always import bar using from mypackage import bar, and execute foo.py in such a way that mypackage is visible automatically:

	$ cd <path containing mypackage>
	$ python -m mypackage.foo.foo

# warning

how to get stack traces for runtime warnings

	From the docs at: https://docs.djangoproject.com/en/1.4/topics/i18n/timezones/#code

	During development, you can turn such warnings into exceptions and get a traceback by adding the following to your settings file:

	import warnings
	warnings.filterwarnings(
			'error', r"DateTimeField received a naive datetime",
			RuntimeWarning, r'django\.db\.models\.fields')

或强制所有warning抛异常

    import warnings
    warnings.simplefilter("error") # warning抛异常, 便于查看问题

将runtime warning输出到日志

	http://docs.python.org/2/library/warnings.html

	logging.captureWarning(True)


ValueError: Unpaired high surrogate
http://bugs.python.org/issue11489

	>>> print(repr(json.loads(json.dumps({u"my_key": u'\uda00'}))['my_key']))
	ValueError: Unpaired high surrogate

> It's Unicode that considers unpaired surrogates invalid, not UTF-8 by itself.

It's UTF-8 too. See RFC 3629:

   The definition of UTF-8 prohibits encoding character numbers between
   U+D800 and U+DFFF, which are reserved for use with the UTF-16
   encoding form (as surrogate pairs) and do not directly represent
   characters.  When encoding in UTF-8 from UTF-16 data, it is necessary
   to first decode the UTF-16 data to obtain character numbers, which
   are then encoded in UTF-8 as described above.

code

	surrogate = re.compile(r"(?<!\\)\\u([dD][8-9a-fA-F][0-9a-fA-F]{2,2})")
	def replace_surrogates(sample):
		return surrogate.sub('#S\g<1>', sample)


# 类间重用方法

可以这样:

	class A:
		def foo(self):
			pass

		@property
		def bar(self):
			pass

	class B:
		foo = A.__dict__['foo']
		bar = A.bar

不要这样

	class B:
		foo = A.foo

否则 B.foo()会报错

	unbound method foo() must be called with A instance as first argument (got B instance instead)

具体原因

A.foo这样引用会对方法做修改, 具体见 http://stackoverflow.com/questions/114214/class-method-differences-in-python-bound-unbound-and-static

	>>> C.foo
	<unbound method C.foo>
	>>> C.__dict__['foo']
	<function foo at 0x17d05b0>
	As you can see if you access the foo attribute on the class you get back an unbound method, however inside the class storage (the dict) there is a function. Why's that? The reason for this is that the class of your class implements a __getattribute__ that resolves descriptors. Sounds complex, but is not. C.foo is roughly equivalent to this code in that special case:

	>>> C.__dict__['foo'].__get__(None, C)
	<unbound method C.foo>

# unicode

unicodedata

http://www.pythonclub.org/python-basic/encode-detail

	UCS有两种格式：UCS-2和UCS-4。顾名思义，UCS-2就是用两个字节编码，UCS-4就是用4个字节（实际上只用了31位，最高位必须为0）编码。下面让我们做一些简单的数学游戏：
	UCS-2有2^16=65536个码位，UCS-4有2^31=2147483648个码位。
	UCS-4根据最高位为0的最高字节分成2^7=128个group。每个group再根据次高字节分为256个plane。每个plane根据第3个字节分为 256行 (rows)，每行包含256个cells。当然同一行的cells只是最后一个字节不同，其余都相同。
	group 0的plane 0被称作Basic Multilingual Plane, 即BMP。或者说UCS-4中，高两个字节为0的码位被称作BMP。
	将UCS-4的BMP去掉前面的两个零字节就得到了UCS-2。在UCS-2的两个字节前加上两个零字节，就得到了UCS-4的BMP。而目前的UCS-4规范中还没有任何字符被分配在BMP之外。

http://www.cmlenz.net/archives/2008/07/the-truth-about-unicode-in-python

Internal Representation
And finally, there's the way Python represents unicode strings internally. Back in version 2.1, the unicode type in Python was simply a string of 2-byte characters, each character being the unicode code point as short integer (that's UCS-2, not UTF-16). When the unicode standard got extended to supports characters outside the Basic Multilingual Plane (BMP), using fixed-length 2-byte-per-character storage was no longer sufficient. You'd either need to use 4 bytes per character (UTF-32 or UCS-4), or you'd need to do variable-length character encoding (such as UTF-16 with surrogate pairs).

The Python developers chose to go with the former option: using 4 bytes per character in every unicode string. However, this was made a compile-time switch, and the default remains UCS-2 to this day (even though many Linux distributions default to UCS-4). That effectively means that on a UCS-2 Python (the common case), characters outside the BMP will screw up your string operations in the same way that multi-byte characters in UTF-8 screw up naive handling of bytestrings:

>>> char = u"\N{MUSICAL SYMBOL G CLEF}"
>>> len(char)
2
>>> import unicodedata
>>> unicodedata.name(char)
Traceback (most recent call last):
  File "…"
Another result of making this a compile-time switch is that Python extensions written against the C API that are compiled against a UCS-2 Python need to be recompiled to run on UCS-4, and vice versa.

len(uchar)为2的现象在mac上确实这样，但debian server的长度已经为1了

# 全角转半角

http://stackoverflow.com/questions/2422177/python-how-can-i-replace-full-width-characters-with-half-width-characters

	>>> import unicodedata
	>>> foo = u'２３４５６７８９０ＩＢ：'
	>>> unicodedata.normalize('NFKC', foo)
	u'1234567890IB:'


# http request


18
down vote
accepted
Sounds like a job for httplib2

	>>> from httplib2 import Http
	>>> from urllib import urlencode
	>>> h = Http()
	>>> data = dict(name="Joe", comment="A test comment")
	>>> resp, content = h.request("http://bitworking.org/news/223/Meet-Ares", "POST", urlencode(data))
	>>> resp
	{'status': '200', 'transfer-encoding': 'chunked', 'vary': 'Accept-Encoding,User-Agent',
	 'server': 'Apache', 'connection': 'close', 'date': 'Tue, 31 Jul 2007 15:29:52 GMT', 
	 'content-type': 'text/html'}

# time <-> datetime

	time.time() == time.mktime(datetime.now().timetuple())
	datetime.fromtimestamp(time.time())

# date => datetime

	datetime.combine(date, datetime.min.time())

# locals
class和module级别可以修改locals(), 但function基本不能(cpython的限制)

http://docs.python.org/2/library/functions.html#locals
> Note The contents of this dictionary should not be modified; changes may not affect the values of local and free variables used by the interpreter.

	class A:
		for x in ['a', 'b']:
			locals()[x] = 1

等价于

	class A:
		a = 1
		b = 1

http://www.gossamer-threads.com/lists/python/dev/1057531?do=post_view_flat

Tightening up the specification for locals()
An exchange in one of the enum threads prompted me to write down 
something I've occasionally thought about regarding locals(): it is 
currently severely underspecified, and I'd like to make the current 
CPython behaviour part of the language/library specification. (We 
recently found a bug in the interaction between the __prepare__ method 
and lexical closures that was indirectly related to this 
underspecification) 

Specifically, rather than the current vague "post-modification of 
locals may not work", I would like to explicitly document the expected 
behaviour at module, class and function scope (as well as clearly 
documenting the connection between modules, classes and the single- 
and dual-namespace variants of exec() and eval()): 

* at module scope, as well as when using exec() or eval() with a 
single namespace, locals() must return the same thing as globals(), 
which must be the actual execution namespace. Subsequent execution may 
change the contents of the returned mapping, and changes to the 
returned mapping must change the execution environment. 
* at class scope, as well as when using exec() or eval() with separate 
global and local namespaces, locals() must return the specified local 
namespace (which may be supplied by the metaclass __prepare__ method 
in the case of classes). Subsequent execution may change the contents 
of the returned mapping, and changes to the returned mapping must 
change the execution environment. For classes, this mapping will not 
be used as the actual class namespace underlying the defined class 
(the class creation process will copy the contents to a fresh 
dictionary that is only accessible by going through the class 
machinery). 
* at function scope, locals() must return a *snapshot* of the current 
locals and free variables. Subsequent execution must not change the 
contents of the returned mapping and changes to the returned mapping 
must not change the execution environment. 

Rather than adding this low level detail to the library reference 
docs, I would suggest adding it to the data model section of the 
language reference, with a link to the appropriate section from the 
docs for the locals() builtin. The warning in the locals() docs would 
be softened to indicate that modifications won't work at function 
scope, but are supported at module and class scope. 

# pypy
http://alexgaynor.net/2010/may/15/pypy-future-python/
http://speed.pypy.org/
> It depends greatly on the type of task being performed. The geometric average of all benchmarks is 0.17 or 5.9 times faster than CPython

# class

	class SubClass(EntityResource):
		A=1
		B=2

that would be translated to:

	 SubClass = type('SubClass', (EntityResource,), {'A': 1, 'B': 2})

# old/new style class

http://stackoverflow.com/questions/54867/old-style-and-new-style-classes-in-python

> Up to Python 2.1, old-style classes were the only flavour available to the user. The concept of (old-style) class is unrelated to the concept of type: if x is an instance of an old-style class, then x.__class__ designates the class of x, but type(x) is always <type 'instance'>. This reflects the fact that all old-style instances, independently of their class, are implemented with a single built-in type, called instance.

Declaration-wise:

New-style classes inherit from object, or from another new-style class.

	class NewStyleClass(object):
		pass

	class AnotherNewStyleClass(NewStyleClass):
		pass

Old-style classes don't.

	class OldStyleClass():
		pass

# decorator

http://stackoverflow.com/questions/739654/how-can-i-make-a-chain-of-function-decorators-in-python#answer-739665

decorator with arguments

	def decorator_maker_with_arguments(decorator_arg1, decorator_arg2):
		def my_decorator(func):
			print "I am the decorator. Somehow you passed me arguments:", decorator_arg1, decorator_arg2

			# Don't confuse decorator arguments and function arguments!
			def wrapped(function_arg1, function_arg2) :
				print ("I am the wrapper around the decorated function.\n"
					  "I can access all the variables\n"
					  "\t- from the decorator: {0} {1}\n"
					  "\t- from the function call: {2} {3}\n"
					  "Then I can pass them to the decorated function"
					  .format(decorator_arg1, decorator_arg2,
							  function_arg1, function_arg2))
				return func(function_arg1, function_arg2)

			return wrapped

		return my_decorator

	@decorator_maker_with_arguments("Leonard", "Sheldon")
	def decorated_function_with_arguments(function_arg1, function_arg2):
		print ("I am the decorated function and only knows about my arguments: {0}"
			   " {1}".format(function_arg1, function_arg2))

# multiple inheritance

depth-first, left-to-right.

http://docs.python.org/2/tutorial/classes.html#multiple-inheritance

bases定义的顺序, general/interface的放后面, 如

	class SubFoo(Foo, object):
		pass

# MySQLdb

http://mysql-python.sourceforge.net/MySQLdb.html#connection-objects

# style guide
http://www.python.org/dev/peps/pep-0008/

> The preferred way of wrapping long lines is by using Python's implied line continuation inside parentheses, brackets and braces. Long lines can be broken over multiple lines by wrapping expressions in parentheses. These should be used in preference to using a backslash for line continuation. Make sure to indent the continued line appropriately. The preferred place to break around a binary operator is after the operator, not before it.

# split list to chunks

	chunks=[data[x:x+100] for x in xrange(0, len(data), 100)]

# python db api (mysqldb)
http://www.python.org/dev/peps/pep-0249/


