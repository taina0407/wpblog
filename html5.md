p不能包含heading (h1-h6)
heading为block元素
http://www.w3.org/TR/2010/WD-html-markup-20101019/h1.html

heading只能包含 [phrasing elements](http://www.w3.org/TR/2010/WD-html-markup-20101019/common-models.html#common.elem.phrasing)

[html elements](http://www.w3.org/TR/2010/WD-html-markup-20101019/elements.html)

p和heading都只能包含[phrasing content](http://www.w3.org/TR/2010/WD-html-markup-20101019/terminology.html#phrasing-content), 因此不能互相包含
inline content在html5中rename为phrasing content
block content在html5中rename为block content
p和heading都是[flow elements](http://www.w3.org/TR/2010/WD-html-markup-20101019/common-models.html#common.elem.flow)

[HTML: The Markup Language Reference](http://www.w3.org/TR/2010/WD-html-markup-20101019/Overview.html)

[elements by function](http://www.w3.org/TR/2010/WD-html-markup-20101019/elements-by-function.html)

[a](http://www.w3.org/TR/2010/WD-html-markup-20101019/a.html)是[transparent](http://www.w3.org/TR/2010/WD-html-markup-20101019/terminology.html#transparent)的, 其能包含的内容由其父容器决定

[html syntax](http://www.w3.org/TR/2010/WD-html-markup-20101019/syntax.html#syntax)

[void element](http://www.w3.org/TR/2010/WD-html-markup-20101019/syntax.html#void-element)不允许包含内容
The following is a complete list of the void elements in HTML:
	area, base, br, col, command, embed, hr, img, input, keygen, link, meta, param, source, wbr
Void elements only have a start tag; end tags must not be specified for void elements.
A non-void element must have an end tag, unless the subsection for that element in the HTML elements section of this reference indicates that its end tag can be omitted.

4.5. Text and character data
Text in element contents (including in comments) and attribute values must consist of Unicode characters, with the following restrictions:

must not contain U+0000 characters
must not contain permanently undefined Unicode characters
must not contain control characters other than space characters

上述文档仅参考。最新文档见
http://docs.webplatform.org/wiki/Main_Page
http://docs.webplatform.org/wiki/html/elements

[html living standard](http://www.whatwg.org/specs/web-apps/current-work/multipage/)
[content model](http://www.whatwg.org/specs/web-apps/current-work/multipage/elements.html#content-models)

> As a general rule, elements whose content model allows any phrasing content should have either at least one descendant Text node that is not inter-element whitespace, or at least one descendant element node that is embedded content.

[paragraphs](http://www.whatwg.org/specs/web-apps/current-work/multipage/elements.html#paragraphs)


http://www.w3.org/TR/html4/charset.html#spec-char-encoding

http://www.epoch-calendar.com/support/javascript_charsets.html
> If your page is already served as UTF-8 (i.e. Content-type=text/html; charset=UTF-8), you don't need to make any changes — all embedded files in an HTML document are served in the same charset as the document, unless explicitly specified not to by you.

http://www.w3schools.com/tags/att_script_charset.asp
> The charset attribute specifies the character encoding used in an external script file.
The charset attribute is used when the character encoding in an external script file differs from the encoding in the HTML document.
oIf the script element has a charset attribute, then let the script block's character encoding for this script element be the result of getting an encoding from the value of the charset attribute.

http://www.whatwg.org/specs/web-apps/current-work/multipage/scripting-1.html#attr-script-charset

> The charset attribute gives the character encoding of the external script resource. The attribute must not be specified if the src attribute is not present
> If the script element has a charset attribute, then let the script block's character encoding for this script element be the result of getting an encoding from the value of the charset attribute.
Otherwise, let the script block's fallback character encoding for this script element be the same as the encoding of the document itself.
The contents of that file, interpreted as a Unicode string, are the script source.

external script会被decode为unicode

> To obtain the Unicode string, the user agent run the following steps:
> If the resource's Content Type metadata, if any, specifies a character encoding, and the user agent supports that encoding, then let character encoding be that encoding, and jump to the bottom step in this series of steps.
> If the algorithm above set the script block's character encoding, then let character encoding be that encoding, and jump to the bottom step in this series of steps.
> Let character encoding be the script block's fallback character encoding.
> If the specification for the script block's type gives specific rules for decoding files in that format to Unicode, follow them, using character encoding as the character encoding specified by higher-level protocols, if necessary.
> Otherwise, decode the file to Unicode, using character encoding as the fallback encoding.


