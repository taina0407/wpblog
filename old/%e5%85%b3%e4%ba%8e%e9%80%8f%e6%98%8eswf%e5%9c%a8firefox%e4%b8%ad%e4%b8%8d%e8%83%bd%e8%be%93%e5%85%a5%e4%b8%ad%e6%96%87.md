### >关于透明SWF在firefox中不能输入中文

  
>网上一篇文章谈的了这个问题 问题：一个SWF文件如果设置为透明属性，在Firefox中无法输入中文 如果不设置为透明属性，则因为SWF默认是顶级z-index，会挡住其他任何层 对于做弹出效果的页面来说，就是灾难 这个问题从05年甚至更早就有了 至今Firefox已经到了3，Flash Player也已经10了 可还是没有解决 而且是官方承认的bug 对于国外公司不重视中文使用者的做法表示抗议 :) 解决办法就是用js建立iframe来做弹出效果（没有办法的办法） 现在找到一个js库，没时间研究，先mark一下 连接地址是： http://jquery.com/demo/thickbox/ 另给一片关于flash wmode的文章 [Flash, DHTML Menus and Accessibility][1]

 [1]: http://www.communitymx.com/content/article.cfm?cid=E5141