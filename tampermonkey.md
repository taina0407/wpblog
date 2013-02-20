http://tampermonkey.net/documentation.php

	GM_addStyle("" + <><![CDATA[ 
	S 
	T 
	U 
	F 
	F 
	]]></> 
	);

http://wiki.greasespot.net/Multi_Line_Strings

> Using @resource
> 
> If the string is too long or too big — e.g. CSS code, JSON data — you can use @resource metadata in your script and get its content using GM_getResourceText API.
> First, add @resource in metadata block
> // @resource resourceName http://www.example.com/example.ext

> GM_addStyle(GM_getResourceText("resourceName")); // if resource content is CSS

// @resource 被缓存了, 若要更新resource, 则需修改url
