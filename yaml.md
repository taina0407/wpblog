
	Valid keywords for the method def dump(data, stream=None, Dumper=Dumper, **kwds) in python-yaml :

	default_style : indicates the style of the scalar. Possible values are None, '', '\'', '"', '|', '>'.
	default_flow_style :  indicates if a collection is block or flow. The possible values are None, True, False.
	canonical : if True export tag type to the output file
	indent :  sets the preferred indentation
	width : set the preferred line width
	allow_unicode : allow unicode in output file
	line_break : specify the line break you need
	encoding : output encoding, defaults to utf-8
	explicit_start : if True, adds an explicit start using “—”
	explicit_end: if True, adds an explicit end using “—”
	version : version of the YAML parser, tuple (major, minor), supports only major version 1
	tags : I didn’t find any information about this parameter … and no time to test it


	Scalars

	There are 5 styles of scalars in YAML: plain, single-quoted, double-quoted, literal, and folded:

	# YAML
	plain: Scroll of Remove Curse
	single-quoted: 'EASY_KNOW'
	double-quoted: "?"
	literal: |    # Borrowed from http://www.kersbergen.com/flump/religion.html
	  by hjw              ___
		 __              /.-.\
		/  )_____________\\  Y
	   /_ /=== == === === =\ _\_
	  ( /)=== == === === == Y   \
	   `-------------------(  o  )
							\___/
	folded: >
	  It removes all ordinary curses from all equipped items.
	  Heavy or permanent curses are unaffected.
	# Python
	{'plain': 'Scroll of Remove Curse',
	'literal':
		'by hjw              ___\n'
		'   __              /.-.\\\n'
		'  /  )_____________\\\\  Y\n'
		' /_ /=== == === === =\\ _\\_\n'
		'( /)=== == === === == Y   \\\n'
		' `-------------------(  o  )\n'
		'                      \\___/\n',
	'single-quoted': 'EASY_KNOW',
	'double-quoted': '?',
	'folded': 'It removes all ordinary curses from all equipped items. Heavy or permanent curses are unaffected.\n'}
	Each style has its own quirks. A plain scalar does not use indicators to denote its start and end, therefore it's the most restricted style. Its natural applications are names of attributes and parameters.

	Using single-quoted scalars, you may express any value that does not contain special characters. No escaping occurs for single quoted scalars except that a pair of adjacent quotes '' is replaced with a lone single quote '.

	Double-quoted is the most powerful style and the only style that can express any scalar value. Double-quoted scalars allow escaping. Using escaping sequences \x** and \u****, you may express any ASCII or Unicode character.

	There are two kind of block scalar styles: literal and folded. The literal style is the most suitable style for large block of text such as source code. The folded style is similar to the literal style, but two adjacent non-empty lines are joined to a single line separated by a space character.
