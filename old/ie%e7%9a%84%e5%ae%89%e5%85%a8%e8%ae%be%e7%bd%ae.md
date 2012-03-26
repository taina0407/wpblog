### >IE的安全设置

  
>1. 直接的ip(包括127.0.0.1）以及域名(www.puppy.com)被视为internet, 机器名(puppy, localhost)被视为intranet 2. 若当"Allow script-initiated windows without size or position constraints"被disable, 则当弹出窗口位置非0,0时，window.resizeTo的行为不正常。（窗口的大小会因resize时窗口的位置不同而有所不同) 3. IE6有选项不允许禁用状态栏，IE7有选项不允许禁用地址栏和状态栏。具体见http://blogs.msdn.com/ie/archive/2006/08/25/719355.aspx