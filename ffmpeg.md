本文环境ubuntu 10.04 server

ubuntu默认不支持amr音频，

	ffmpeg -i 1.amr 1.mp3

	Unsupported codec (id=73728) for input stream #0.1

参考下面的文章
HOWTO: Easily enable MP3, MPEG4, AAC, and other restricted encoders in FFmpeg

http://perpustakaan.kemdiknas.go.id/ridho/?p=62

C. 使用medibuntu

medibuntu的安装

https://help.ubuntu.com/community/Medibuntu

	sudo wget --output-document=/etc/apt/sources.list.d/medibuntu.list http://www.medibuntu.org/sources.list.d/$(lsb_release -cs).list && sudo apt-get --quiet update && sudo apt-get --yes --quiet --allow-unauthenticated install medibuntu-keyring && sudo apt-get --quiet update

安装 ffmpeg libavcodec-extra-52

	sudo apt-get install ffmpeg libavcodec-extra-52

libavcodec-extra-52必须使用medibuntu的版本，ubuntu自己的版本不支持amr等格式

> This package does not support libfaac (an AAC encoder), AMR-NB encoding, and AMR-WB decoding. I recommend using libavcodec-extra-52 from the Medibuntu repository if you need this functionality. See option C. Medibuntu.

装完之后

	$ ffmpeg -formats | grep amr
	DE amr             3GPP AMR file format
	DEA    libopencore_amrnb OpenCORE Adaptive Multi-Rate (AMR) Narrow-Band
	D A    libopencore_amrwb OpenCORE Adaptive Multi-Rate (AMR) Wide-Band

若没装medibuntu的libavcodec-extra-52

	$ ffmpeg -formats | grep amr
	DE amr             3GPP AMR file format

A. 从源码编译

https://ffmpeg.org/trac/ffmpeg/wiki/UbuntuCompilationGuideLucid


上面的答案从以下帖子中找到的

http://ubuntuforums.org/showthread.php?t=1961328&page=3

Quote:
Originally Posted by doru001  
Is "ffmpeg -i in.amr out.wav" supposed to work?
Yes, but only if you follow option A or C from HOWTO: Easily enable MP3, MPEG4, AAC, and other restricted encoders in FFmpeg.

Quote:
Originally Posted by doru001  
Is medibuntu required for amr?
Yes, if you use ffmpeg from the repository.
No, if you compile ffmpeg.

Quote:
Originally Posted by doru001  
Why is "ffmpeg -i in.amr -acodec copy out.wav" working while "ffmpeg -i in.amr out.wav" is not?
In the first command ffmpeg is blindly copying the audio from the input and dumping it into the output. You could probably do this with a variety of output formats despite them being not a valid or supported combination. The second command is attempting to re-encode, but it fails because you do not have an AMR decoder.

To get AMR decoding working you have two main options: you can compile ffmpeg (see option A in above link), or, if you want to use repository ffmpeg, use the libavcodec-extra-52 package from Medibuntu (see option C in above link). Then in ffmpeg -formats, under the Codecs section (I'm talking about repo version here), you should see:
Code:
DEA    libopencore_amrnb OpenCORE Adaptive Multi-Rate (AMR) Narrow-Band
D A    libopencore_amrwb OpenCORE Adaptive Multi-Rate (AMR) Wide-Band

	I followed the instructions on this page: http://ubuntuforums.org/showpost.php...postcount=1289 and now ffmpeg can use the amr codec:
	Code:
	ffmpeg -i file.amr file.wav
	works. In the new version, use: 
	Code:
	ffmpeg -codecs
	and not 
	ffmpeg -formats
	to display available codecs. 

	I wish to thank everybody who posted here and especially FakeOutdoorsman who helped me understand basic usage of ffmpeg. I also thank ron999 and mc4man who encouraged me to compile ffmpeg from source. Compilation took over 3 hours on my slow unicore computer.



