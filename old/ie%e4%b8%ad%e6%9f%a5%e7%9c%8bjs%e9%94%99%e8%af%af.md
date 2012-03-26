### >IE中查看js错误

  
>在IE中报js错误时，虽然也给了错误的代码行数，但并没给出具体出错的文件。通过安装Windows Script Debugger和ComanionJS，能准确定位到出错的js并查看调用栈。具体步骤如下 1. 安装Windows Script Debugger, 微软主页上下载需要正版验证，可从[这里][1]下载. 2. 将 工具 > Internet选项 > 高级 中的 禁用脚本调试 两个选项设置为未选中状态 3. 安装[DebugBar][2]，需要穿墙，推荐使用无界 4. 安装[CompanionJS][3] 5. 重启IE 如果页面有js错误，会在页面左上角显示一个alert窗口，双击就可以定位到错误代码 ![][4] 备注， 1. 除了Windows Script Debugger外，也可以装其他调试工具如Windows Script Editor等，见[Scripting Debugging in Internet Explorer][5] 只有装了其中某个调试工具后，IE中的 查看 栏里才会有"脚本调试工具"这个选项. 但我自己装了frontpage后，并没有Windows Script Editor，不知道是设置问题还是我使用的office安装程序有问题，只好换成了可独立安装的Windows Script Debugger. 2. 除了DebugBar和CompanionJS外，[DebugBar][6]公司的另一个工具<IETester>也很好用，可同时测试IE5.5/6/7/8的渲染以及javascript引擎。 ![][7] 3. IE中还有很多其他方便的工具，我还没有一一试用，见[Web Development Tools for Internet Explorer][8] 4. IE中常见的一个js错误是定义对象时不能有最后一个逗号（firefox可以有)，如 
<pre>var obj = {
  name: "ping",
  gender: 'male', //这里应该去掉','，否则IE报js错
};
</pre>

 [1]: http://download.microsoft.com/download/7/7/d/77d8df05-6fbc-4718-a319-be14317a6811/scd10en.exe
 [2]: http://www.debugbar.com/download.php
 [3]: http://www.my-debugbar.com/wiki/CompanionJS/HomePage
 [4]: http://www.my-debugbar.com/wiki/uploads/CompanionJS/introducing-cjs.png
 [5]: http://www.jonathanboutelle.com/mt/archives/2006/01/howto_debug_jav.html
 [6]: http://www.debugbar.com/?langage=en
 [7]: http://www.my-debugbar.com/wiki/uploads/IETester/ietester-0.3.png
 [8]: http://blogs.msdn.com/ie/archive/2007/06/22/from-microsoft-teched-2007-web-development-tools-for-internet-explorer.aspx