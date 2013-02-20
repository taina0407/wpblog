http://effbot.org/zone/element-index.htm

	The Element type is a simple but flexible container object, designed to store hierarchical data structures, such as simplified XML infosets, in memory. The element type can be described as a cross between a Python list and a Python dictionary.

	The ElementTree wrapper type adds code to load XML files as trees of Element objects, and save them back again.

	The Element type is available as a pure-Python implementation for Python 1.5.2 and later. A C implementation is also available, for use with CPython 2.1 and later. The core components of both libraries are also shipped with Python 2.5 and later.

	There’s also an independent implementation, lxml.etree, based on the well-known libxml2/libxslt libraries. This adds full support for XSLT, XPath, and more.
	http://lxml.de/tutorial.html#the-element-class

> An Element is the main container object for the ElementTree API. Most of the XML tree functionality is accessed through this class

> An ElementTree is mainly a document wrapper around a tree with a root node. It provides a couple of methods for serialisation and general document handling.

> One of the important differences is that the ElementTree class serialises as a complete document, as opposed to a single Element. This includes top-level processing instructions and comments, as well as a DOCTYPE and other DTD content in the document:

	child.tag for child in root
	child.getparent().tag

# Element methods

	http://effbot.org/zone/pythondoc-elementtree-ElementTree.htm#elementtree.ElementTree._ElementInterface-class

	clear() 
		Resets an element. This function removes all subelements, clears all attributes, and sets the text and tail attributes to None.
	insert(index, element)
		Inserts a subelement at the given position in this element.
	el.append(element) - append到元素内部末尾
		Adds a subelement to the end of this element.
	remove(element) - 移除element, 包括element.tail
	set(key, value)
		Sets an element attribute
	attrib # 可替换, 不可修改 (修改用set)
		(Attribute) Element attribute dictionary. Where possible, use get, set, keys, and items to access element attributes.
	getchildren():
		返回所有儿子节点
	getiterator(tag=None) 或 iter()
		文档序遍历所有元素, 包括所有后代节点
		If the tree structure is modified during iteration, the result is undefined.

对下述html

	<div>div text<a>xx</a>a tail</div>
tail, text如下

	div.text == 'div text'
	a.tail == 'a tail'

http://lxml.de/lxmlhtml.html#html-element-methods

> HTML elements have all the methods that come with ElementTree, but also include some extra methods:

	el.drop_tree() - 移除el(不包括el.tail)
	el.getparent().remove(el) # 移除el以及el.tail
	el.drop_tag() # Drops the tag, but keeps its children and text.
	el.text_content() # 获取el的所有文本内容(包括所有后代节点)

不能在iter遍历的同时修改文档

	for child in node.iter():
		child.drop_tree()

这样不会遍历到所有节点, 可以这样

	for child in list(node.iter()):
		child.drop_tree()

http://lxml.de/parsing.html#parsing-html

创建文档

	import lxml.html
	lxml.html.fromstring('')
	
	document_fromstring - return ElementTree, 能用Element的所有方法(.tag == 'html')
	fragment_fromstring - return Element
	fragments_fromstring - return [Element,...]
	fromstring - return Element or ElementTree
	
	lxml.html.tostring(elem)



# 文档中包含 < 的处理
http://stackoverflow.com/questions/14171035/lxml-truncates-text-that-contains-less-than-character

	'<div> < 20 </div>'

lxml.html.fragment_fromstring不行
得用lxml.html.html5parser.fragment_fromstring

# unicode

http://lxml.de/parsing.html#python-unicode-strings
> you will get errors when you try the same with HTML data in a unicode string that specifies a charset in a meta tag of the header. You should generally avoid converting XML/HTML data to unicode before passing it into the parsers. It is both slower and error prone.
