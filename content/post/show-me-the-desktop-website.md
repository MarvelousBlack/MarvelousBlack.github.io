---
title: "让我康康电脑版网页"
date: 2019-06-04T12:58:12+08:00
description: "最近交网费的时候遇到一个神奇的现象，无论咱怎么开都显示手机页面，于是寻找了一下原因接着就写一下。"
topics:
  - 杂乱的笔记
tags:
  - 碎碎念
draft:  false
---
注：其實咱也不知道要怎麼解決這個問題，如果有詳細的解決方案可以留言給咱。感激不盡。

## 背景
前兩天咱還想起咱網費還沒交，快要停網了，於是去交一下。本來也沒什麼事情的，但是，當我在 **Arch Linux** 下打開 **chromium** 打開繳費頁面，它展示了一個手機頁面給咱。嘛，手機就手機頁面嘛，沒什麼問題，趕緊交完就完事了。But，汝覺得就真的這麼簡單就完事了嗎？對沒錯，咱學校的那個繳費頁面只能在電腦端繳費，而手機端確實可以提交訂單，但不能選擇任何一個支付方式，對沒看錯，確實有訂單，但咱就是支付不了。這個還是咱問了一下同學才知道，只有電腦端頁面才能打開，那麼就打開電腦端唄。

## 尋找原因
於是咱想，應該是判斷了 UA 於是把 UA 換成了 IE10 的 UA 心想這個應該沒問題吧，刷新之後依舊，這就說明不是靠 UA 做判斷的，但爲了先繳費還是先回到 windows 下繳費，在 win 下用 chrome 打開,居然顯示了正確的電腦端的頁面，說明肯定有些奇怪的東西。於是按下 F12 開始找那堆js看，終於被咱找到了一個叫 `common.js` 的文件，在這個文件裏面有幾行神奇（真的神奇）的代碼。這就是罪魁禍首。下面是這個文件的截取（註釋不是咱寫的而是本來就有的）：
```
//手机页面自动跳转手机版收费平台
function mobile_device_detect(url) {
	var thisOS = navigator.platform;
	var os = new Array("iPhone", "iPod", "iPad", "android", "Nokia",
			"SymbianOS", "Symbian", "Windows Phone", "Phone", "Linux armv71",
			"MAUI", "UNTRUSTED/1.0", "Windows CE", "BlackBerry", "IEMobile");
	for (var i = 0; i < os.length; i++) {
		if (thisOS.match(os[i])) {
			window.location = url;
		}
	}
	// 因为相当部分的手机系统不知道信息,这里是做临时性特殊辨认
	if (navigator.platform.indexOf('iPad') != -1) {
		window.location = url;
	}
	// 做这一部分是因为Android手机的内核也是Linux
	// 但是navigator.platform显示信息不尽相同情况繁多,因此从浏览器下手，即用navigator.appVersion信息做判断
	var check = navigator.appVersion;
	if (check.match(/linux/i)) {
		// X11是UC浏览器的平台 ，如果有其他特殊浏览器也可以附加上条件
		if (check.match(/mobile/i) || check.match(/X11/i)) {
			window.location = url;
		}
	}
	// 类in_array函数
	Array.prototype.in_array = function(e) {
		for (i = 0; i < this.length; i++) {
			if (this[i] == e)
				return true;
		}
		return false;
	}
}
```
這是啥噢，還記得咱前面加粗了 linux 嗎？對沒錯這個代碼真的是坑人，有 linux 關鍵字下面還判斷了一下 x11 。誰告訴汝 X11 是 UC 瀏覽器！！！X11是個啥東東？可以康康[wiki](https://en.wikipedia.org/wiki/X_Window_System) 是這個東西：

> The X Window System (X11, or simply X) is a windowing system for bitmap displays, common on Unix-like operating systems. 

也就是說，在一般的 linux 桌面環境下，只要瀏覽器給出了正確的值，基本上都會被這個判斷爲手機頁面。但這個支付網頁的手機頁面跟本就不能用的，因爲最最最最基本的功能都沒有，這個手機頁面只能看，沒法支付啊。咱還看了一下這個文件有兩個地址`http://xxxxxx/js/common.js`和`http://xxxxxx/NetWorkUI/js/common.js`（因爲某些原因咱用 xxxxxx 代替了原有的域名），而且這文件上面還有一些其他的函數是頁面需要的，因此不能直接的將整個 js 文件屏蔽掉。真的麻煩惹，而調用這個函數的js是直接嵌在 html 中的，於是頁面加載的時候就開始跳轉了，油猴似乎沒辦法對付這種情況，咱還想不到有什麼辦法可以跳過這個噁心的代碼。

## 後記
如果有詳細的解決方案可以留言給咱。如果上面的解釋有什麼錯誤的地方也請及時反饋給咱，咱會馬上更正過來，因爲咱也不太懂網頁這些東西。但確實遇到了這樣的問題，於是就先記錄下來了。
