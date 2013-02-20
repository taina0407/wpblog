http://oreilly.com/server-administration/excerpts/9780596522315/optimizing-images.html
https://github.com/thebeansgroup/smush.py/blob/master/smush/smush.py
https://kraken.io

http://effbot.org/imagingbook/image.htm
http://effbot.org/imagingbook/format-jpeg.htm
> PIL reads JPEG, JFIF, and Adobe JPEG files containing “L”, “RGB”, or “CMYK” data. It writes standard and progressive JFIF files.

http://docs.wand-py.org/
> Wand is a ctypes-based simple ImageMagick binding for Python.

http://pythonpath.wordpress.com/2013/04/01/working-with-pil-and-exif/

> PIL does not automatically save EXIF and PIL’s exif methods do not grab everything

	# After editing/manipulating image with PIL
	savename = filename[:-4]+'_new.jpg'
	image.save( savename, quality=95 ) # Default quality is 75
	subprocess.call(['exiftool.exe',
					 savename,
					 '-tagsFromFile',
					 filename], shell=True)

https://github.com/wiredfool/Pillow/commit/c68044bf7fa63f7e6642393cb4ea344753c34d35
> Fix IOError when saving progressive JPEGs.
> when the jpeg encoder sees the flags optimize or progressive (or progression)
> it will write the full image in one shot.
> 
> The bufsize needs to be big enough to hold the entire image. The current heuristic
> is that the entire compressed image will fit in width * height bytes, but this
> heuristic is only applied to save operations with the flag "optimize" and not to
> save operations with the flag "progressive".
> 
> This patch fixes this oversight.
> 
> (Btw, it will probably be a good idea to have a loop that retries with a bigger
> bufsize in case this guess is not big enough.)

jpeg图片的压缩包括

- 每张图片使用单独的huffman码表 (libjpeg 8c及以后的版本已经支持)
- 大于10K的图片使用progressive模式
- 去除图片的metadata (如exif, 预览图等)
- 合适的质量因子 (quality, 0-100, PIL的默认值为75)

python的[PIL](http://effbot.org/imagingbook/image.htm)已经能够支持上述所有压缩方式，示例代码如下

	def optimize_jpeg(src_path, dest_path, quality=75, progressive=True, optimize=True):
		'''
		quality - 质量因子, 0 - 100
		optimize - 是否进行huffman码表优化
		progressive - 是否使用progressive模式    '''
		import PIL
		from PIL import ImageFile, Image
		from exceptions import IOError
		image = Image.open(src_path)
		try:
			# see https://github.com/wiredfool/Pillow/commit/c68044bf7fa63f7e6642393cb4ea344753c34d35
			ImageFile.MAXBLOCK = max(512*1024, image.size[0] * image.size[1])
			kwargs = dict(quality=quality)
			# PIL只检测progressive和optimize是否存在而不检测具体的值，所以不要这么写: kwargs['progressive'] = False
			if progressive:
				kwargs['progressive'] = True
			if optimize:
				kwargs['optimize'] = True
			image.save(dest_path, "JPEG", **kwargs)
		except IOError:
			image.save(dest_path, "JPEG", quality=quality)

相关链接

[Optimizing Images: Chapter 10 - Even Faster Websites](http://oreilly.com/server-administration/excerpts/9780596522315/optimizing-images.html)
https://kraken.io
http://pythonpath.wordpress.com/2013/04/01/working-with-pil-and-exif/
https://github.com/thebeansgroup/smush.py/blob/master/smush/smush.py
http://docs.wand-py.org/
