https://makandracards.com/magento/7391-restoring-console-log

	if (!window.console || !window.console.log) {
		// 有的网站会将window.console替换掉, 导致tampermonkey抛异常, 因此restore回来
		var iframe = document.createElement('iframe');
		iframe.style.display = 'none';
		document.getElementsByTagName('body')[0].appendChild(iframe);
		window.console = iframe.contentWindow.console;
		console.log('original console restored');
	}
