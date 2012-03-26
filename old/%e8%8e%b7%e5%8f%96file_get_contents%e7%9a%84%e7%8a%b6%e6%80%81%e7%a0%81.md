### >获取file\_get\_contents的状态码

  
>使用$http\_response\_header这个全局变量 <pre class="code">if (false === file_get_contents('http://example.com')) {
    list($version,$status_code,$msg) = explode(' ',$http_response_header[0], 3);
}
</pre> 参考

[BeTwittered, PHP and file\_get\_contents][1]

 [1]: http://robert.arlesnet.com/2008/01/20/betwittered-php-and-file_get_contents/