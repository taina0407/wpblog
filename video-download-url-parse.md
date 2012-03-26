[土豆视频真实地址解析](http://www.douban.com/note/152544196/)
土豆视频的解析相比于优酷的解析，因为地址没有加密，显得更直接。但是在服务器端限制了请求用户和下载用户的user-agent，需要特别注意。可以利用切换用户user-agent的工具，改变自己的user-agent然后发送请求，得到具有针对性的下载地址。

直接改 User Agent 成ipad的，土豆优酷会返回mp4的文件

[点量视频真实视频地址解析嗅探组件](http://hi.baidu.com/dlbtsoft/blog/item/5ca7deb5dab835648ad4b22f.html)

http://hi.baidu.com/zhangkui416/blog/item/d23b1517263bdf02972b43fc.html


[土豆 56等网站FLV视频下载解析－使用专业下载工具“维棠”](http://hi.baidu.com/%D0%C4%D2%C1%D4%B0/blog/item/3f0f6b165d1a0d01c93d6dd3.html)

[FlV视频播放器代码 土豆，优酷，酷6 ，偶偶，56等网站flv视频下载+转换](http://hi.baidu.com/huanjihou/blog/item/44456d6c12c139ed43169499.html)

good [优酷视频真实地址解析](http://3shi.net/analyze-youku-video-address/)
实际测试了下，有关多分段文件的下载，现在算法有点小变化。
生成的realId的第10位代表的是分段的片段，比如
Part3(from 0)-’03000104034E4F1D4FB22C004861ABF82FD867′,Part2-’03000104024E4F1D4FB22C004

tinshuang 于 2011 年 10 月 13 日 00:23
楼主，key的计算方法现在好像不适用了。K=的值并好像加密了。
回复
3shi 于 2011 年 10 月 15 日 13:58
是吗，很久没有研究优酷的算法了，变动好像很大。等有时间了再好好研究下。

tatters 于 2011 年 10 月 27 日 19:41
刚刚发现，K值直接在“http://v.youku.com/player/getPlayList/VideoIDS/XMjUyODAzNDg0”现实出来了！

http://v.youku.com/player/getPlayList/VideoIDS/XMjUyODAzNDg0

http://3shi.net/analyze-ku6-video-address/
酷6已经更新了开放平台，视频地址的获取也有了官方的相关接口http://dev.ku6.com/?q=node/17，感兴趣的朋友不妨去看一下。
http://v.ku6.com/fetch.htm?t=getVideo4Player&vid=ovUg4EOBDFCq8mmn

http://3shi.net/analyze-letv-video-address/
http://3shi.net/analyze-tudou-video-address/

请问，如果想分析56的视频，应该怎么做呢？
回复
3shi 于 2011 年 05 月 10 日 17:14
如果是以http://www.56.com/u80/v_NjAzNjM0MDU.html为例
只需访问http://vxml.56.com/json/NjAzNjM0MDU/
其中的rfiles：url就是下载地址
回复
yj0209 于 2011 年 05 月 10 日 17:40
太帅了，3shi大大真是好人       
那个。。再问一个问题哈，怎么能自己找到这些规律呢，不然我再遇到新的网站就还是不知道怎么弄呢~
回复
3shi 于 2011 年 05 月 10 日 20:08
这个主要靠抓包工具来分析，大多数网站firebug已经够用了，遇到youku这样的加密算法，就要反编译它的swf文件，还有经验和运气也很重要 


tudou的页面不能访问了
直接在地址栏打开播放地址不行，必须从某页面点过去
可能是referer保护
http://www.tudou.com/playlist/p/a67282i94072394.html
