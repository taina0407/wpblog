### >命令行创建桌面快捷方式

  
>以下shortcut.bat脚本为脚本所在目录的QQ.exe生成桌面快捷方式 <pre class="code">Sub bat
 echo off & cls
 pushd "%~dp0"
 echo create_shortcut
 start wscript -e:vbs "%~f0"
 popd
 Exit Sub
End Sub

Sub shortcut(name)
 Set WshShell = WScript.CreateObject("WScript.Shell")
 strCurrDir = WshShell.currentdirectory
 strDesktop = WshShell.SpecialFolders("Desktop")
 set oShellLink = WshShell.CreateShortcut(strDesktop & "\" & name & ".lnk")
 oShellLink.TargetPath = strCurrDir & "\" & name & ".exe"
 oShellLink.WindowStyle = 3
 oShellLink.IconLocation = oShellLink.TargetPath & ", 0"
 oShellLink.Description = "快捷方式"
 oShellLink.WorkingDirectory = strCurrDir
 oShellLink.Save 
End Sub

shortcut "QQ"
</pre> 参考 

[vb和dos批处理创建或生成快捷方式][1] [通过批处理创建桌面快捷方式.bat][2]

 [1]: http://www.cnblogs.com/gszhl/archive/2009/04/23/1441753.html
 [2]: http://hi.baidu.com/licaiyi28/blog/item/8a2ebe95a248fe0c7bf48006.html