reset - 终端显示混乱后重置状态

man readline

ctrl-a - start of line
ctrl-e - end of line

   Commands for Moving
       beginning-of-line (C-a)
       end-of-line (C-e)
       forward-char (C-f)
       backward-char (C-b)
       forward-word (M-f)
              Move forward to the end of the next word.  Words are composed of alphanumeric characters (letters and digits).
       backward-word (M-b)
              Move back to the start of the current or previous word.  Words are composed of alphanumeric characters (letters and digits).
       clear-screen (C-l)
              Clear the screen leaving the current line at the top of the screen.  With an argument, refresh the current line without clearing the screen.
       redraw-current-line
              Refresh the current line.
   Killing and Yanking
       kill-line (C-k)
              Kill the text from point to the end of the line.
       backward-kill-line (C-x Rubout)
              Kill backward to the beginning of the line.
       unix-line-discard (C-u)
              Kill backward from point to the beginning of the line.  The killed text is saved on the kill-ring.
       kill-whole-line
              Kill all characters on the current line, no matter where point is.
       kill-word (M-d)
              Kill from point the end of the current word, or if between words, to the end of the next word.  Word boundaries are the same as those used by forward-word.
       backward-kill-word (M-Rubout)
              Kill the word behind point.  Word boundaries are the same as those used by backward-word.
       unix-word-rubout (C-w)
              Kill the word behind point, using white space as a word boundary.  The killed text is saved on the kill-ring.
       unix-filename-rubout
              Kill the word behind point, using white space and the slash character as the word boundaries.  The killed text is saved on the kill-ring.
       delete-horizontal-space (M-\)
              Delete all spaces and tabs around point.

C-w 向前删除直到空格
M-delete / M-d - 删除prev/next word
C-u, C-k - 删除直到行首/行尾
M-f, M-b - forward/backword a word

c-r


Special Parameters

       *      Expands to the positional parameters, starting from one.  When the expansion occurs within double quotes, it expands to a single word with the value of each parameter separated by the first character of the IFS special
              variable.  That is, "$*" is equivalent to "$1c$2c...", where c is the first character of the value of the IFS variable.  If IFS is unset, the parameters are separated by spaces.  If IFS  is  null,  the  parameters  are
              joined without intervening separators.
       @      Expands  to  the positional parameters, starting from one.  When the expansion occurs within double quotes, each parameter expands to a separate word.  That is, "$@" is equivalent to "$1" "$2" ...  If the double-quoted
              expansion occurs within a word, the expansion of the first parameter is joined with the beginning part of the original word, and the expansion of the last parameter is joined with the last part of  the  original  word.
              When there are no positional parameters, "$@" and $@ expand to nothing (i.e., they are removed).
       #      Expands to the number of positional parameters in decimal.
       ?      Expands to the exit status of the most recently executed foreground pipeline.
       -      Expands to the current option flags as specified upon invocation, by the set builtin command, or those set by the shell itself (such as the -i option).
       $      Expands to the process ID of the shell.  In a () subshell, it expands to the process ID of the current shell, not the subshell.
       !      Expands to the process ID of the most recently executed background (asynchronous) command.
       0      Expands  to  the name of the shell or shell script.  This is set at shell initialization.  If bash is invoked with a file of commands, $0 is set to the name of that file.  If bash is started with the -c option, then $0
              is set to the first argument after the string to be executed, if one is present.  Otherwise, it is set to the file name used to invoke bash, as given by argument zero.
       _      At shell startup, set to the absolute pathname used to invoke the shell or shell script being executed as passed in the environment or argument list.  Subsequently, expands to the last argument to the previous command,
              after expansion.  Also set to the full pathname used to invoke each command executed and placed in the environment exported to that command.  When checking mail, this parameter holds the name of the mail file currently
              being checked.


获取进程的当前目录
pwdx 13456
ls -l /proc/13456/cwd
