### >windir vs. SystemRoot

  
>
> %WinDir% is old dated environment variable for the Windows folder. %SystemRoot% is the newer dated environment variable for the Windows folder. %WinDir% remains in use, to allow batch scripts to run on both NT and 9x systems. If you use an NT command script, then %SystemRoot% would be more suitable to use. I would expect all system environment variables to be available at the same time. Windows NT and Windows 2000 do not have a Windows directory, so %WinDir% would have been strange? They have WinNT folders instead, which may have prompted the change? 
> SYSTEMROOT = System returns the location of the Windows root directory. WINDIR = System returns the location of the OS directory. 
> if you go to: My Computer / Properties / Advanced TAB and click on 'Environment Variables' and scroll the lower window to the bottom, you find 'windir'. Double clicking this results in the windir variable having a value of '%systemroot% leading me to believe %systemroot% is the 'basis' for this variable.