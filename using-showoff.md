
ubuntu 11.04

showoff依赖gli, 但目前(0.7.0)只与gli 1.x系列兼容，装showoff会直接装gli2.0, 因此先直接将1.x装上

	sudo gem install --version '< 2.0' --no-ri --no-rdoc gli

然后

	sudo gem install showoff pdfkit
	sudo aptitude install wkhtmltopdf

没有x环境，要生成pdf需要安装xvfb

	sudo aptitude install xvfb

	sudo su -
	cd /usr/bin
	mv wkhtmltopdf wkhtmltopdf1
	cat > wkhtmltopdf <<EOF
	#!/bin/sh
	xvfb-run wkhtmltopdf1 $*
	EOF
	chmod a+x wkhtmltopdf
	
