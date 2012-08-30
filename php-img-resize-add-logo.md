"=========== Meta ============
"StrID : 67
"Title : php改变图片大小并加logo
"Slug  : 
"Cats  : php
"Tags  : php
"=============================
"EditType   : post
"EditFormat : Markdown
"BlogAddr   : http://blog.pkufranky.com/
"========== Content ==========
工作中需要改变图片大小并加上logo, 且需要保持原始图片的透明性. php的官方文档给出了例子([resize][1], [add logo][2]). 下面是我的实现，将图片resize为300x300, 然后在左下角加上logo (png格式的文件), 最后保存为png格式的图片. 

	:::php
	<?php
	function resize_image($image, $width, $height) {
		$newImg = imagecreatetruecolor($width, $height);

		// for keeping transparent
		imagealphablending($newImg, false);
		imagesavealpha($newImg,true);
		$transparent = imagecolorallocatealpha($newImg, 255, 255, 255, 127);
		imagefilledrectangle($newImg, 0, 0, $width, $height, $transparent);

		imagecopyresampled($newImg, $image, 0, 0, 0, 0,
			$width, $height, $imgInfo[0], $imgInfo[1]);

		return $newImg;
	}

	function add_logo($image, $logo_file) {
		imagealphablending($image, TRUE);
		$logo_img = imagecreatefrompng($logo_file);
		$logow = imagesx($logo_img);
		$logoh = imagesy($logo_img);
		imagecopy($image, $logo_img, 0, imagesy($image) - $logoh, 0, 0,
			$logow, $logoh);
		return $image;
	}

	function get_image_from_file($file) {
		$imgInfo = getimagesize($file);
		$img_type = $imgInfo[2];
		switch ($img_type) {
			case 1: $image = imagecreatefromgif($file); break;
			case 2: $image = imagecreatefromjpeg($file); break;
			case 3: $image = imagecreatefrompng($file); break;
			default: exit('Unsupported filetype!');
		}
		return $image;
	}

	$image = get_image_from_file('abc.jpg');
	$image = resize_image($image, 300, 300);
	$image = add_logo($image, 'logo.png');
	imagepng($image, 'abc.png');

[1]: http://cn2.php.net/manual/en/function.imagecopyresampled.php#104028
[2]: http://www.php.net/manual/en/function.imagecopy.php#62264
