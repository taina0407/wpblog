### >自动部署工具Capistrano

  
>
<pre>之前自己写脚本通过"ssh host cmds"远程执行命令的方式进行自动部署。突然发现一个非常好的自动部署工具
<a href="http://www.capify.org/">Capistrano</a>，考虑以后用这个。

摘录其介绍如下

Capistrano is a utility and framework for executing commands in parallel on multiple remote machines, via SSH. It uses a
simple DSL (borrowed in part from Rake, <a href="http://rake.rubyforge.org/">http://rake.rubyforge.org/</a>) that allows you to define _tasks_, which may be
applied to machines in certain roles. It also supports tunneling connections via some gateway machine to allow
operations to be performed behind VPN's and firewalls.

Capistrano was originally designed to simplify and automate deployment of web applications to distributed environments,
and originally came bundled with a set of tasks designed for deploying Rails applications. The deployment tasks are now
(as of Capistrano 2.0) opt-in and require clients to explicitly put
"load 'deploy'" in their recipes.

下面是其首页上的说明
</pre>

## Capistrano is…

*   *Great for automating tasks* via SSH on remote servers, like *software installation*, *application deployment*, *configuration management*, ad hoc *server monitoring*, and more.
*   *Ideal for system administrators*, whether professional or incidental.
*   *Easy to customize.* Its configuration files use the [Ruby][1] programming language syntax, but you don't need to know Ruby to do most things with <span class="appname">Capistrano.</span>
*   *Easy to extend.* <span class="appname">Capistrano</span> is written in the [Ruby][1] programming language, and may be extended easily by writing additional Ruby modules.

<pre>Capistrano也能利用各种scm进行部署，见<a href="http://github.com/guides/deploying-with-capistrano"><span style="font-size:100%;">Deploying with Capistrano (git)</span></a>
且它是开源的，这是其<a href="http://github.com/jamis/capistrano/tree/master">git repository</a>
</pre>

<h2 class="r">
  <span class="m"> </span>
</h2>

<pre><a href="http://www.scribd.com/doc/1618/a-great-capistrano-cheatsheet">a great capistrano cheatsheet</a>给出了capistran的详细功能列表，虽然是1.x的(目前已经到本2.2.0)
</pre>

<pre></pre>

 [1]: http://www.ruby-lang.org/