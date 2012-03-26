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
