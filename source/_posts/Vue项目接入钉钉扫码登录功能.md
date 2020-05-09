---
title: Vue项目接入钉钉扫码登录功能
date: 2020-05-09 14:23:02
tags:
- 钉钉扫码
comments: true
categories: 
- Vue
---
## Vue项目接入钉钉扫码登录功能

#### 官方开发文档https://open-doc.dingtalk.com/microapp/serverapi2/kymkv6

### 1.注册相关账号获取所需信息,进入钉钉开发平台，登录企业管理员账号，进入https://open-dev.dingtalk.com/#/loginMan
<!-- more -->

### 2.点击创建扫码登录应用授权

### 3.创建成功以后拿到appId和appSecret

### 4.在index.html文件中引入

```
<script src="http://g.alicdn.com/dingding/dinglogin/0.0.5/ddLogin.js"></script>
```

### 5.在登录页实例化js对象

````javascript
mounted(){
 	let appid = 'dingoabpvejde5tflyv4qk';
 	let url = encodeURIComponent("你的url地址");
 	let goto = encodeURIComponent("https://oapi.dingtalk.com/connect/oauth2/sns_authorize?appid="+appid+ "&response_type=code&scope=snsapi_login&state=STATE&redirect_uri=" + url);
 	var obj = DDLogin({
 			id: "login_container",
 			goto: goto,
 			style: "border:none;background-color:rgba(255,255,255,0);",
 			width: "100%",
 			height:'1000px',
 		});
 var hanndleMessage = (event)=> {
 		console.log(event);
 		var origin = event.origin;
 		if (origin === "https://login.dingtalk.com") {
 			//判断是否来自ddLogin扫码事件。
 			var loginTmpCode = event.data;
 			//拿到loginTmpCode后就可以在这里构造跳转链接进行跳转了
 			let url = ("https://oapi.dingtalk.com/connect/oauth2/sns_authorize?		appid="+appid+"&response_type=code&scope=snsapi_login&state=STATE&redirect_uri="+url+"&loginTmpCode="+loginTmpCode)
			location.href = url;
       }

};
        if (typeof window.addEventListener != "undefined") {
            window.addEventListener("message", hanndleMessage, false);
        } else if (typeof window.attachEvent != "undefined") {
            window.attachEvent("onmessage", hanndleMessage);
        }
}
````

### 6.重定向到当前页面拿到code

##### 通过JS获取到loginTmpCode后，需要由你构造并跳转到如下链接：

```
https://oapi.dingtalk.com/connect/oauth2/sns_authorize?appid=APPID&response_type=code&scope=snsapi_login&state=STATE&redirect_uri=REDIRECT_URI&loginTmpCode=loginTmpCode
```

##### 此链接处理成功后，会302跳转到goto参数指定的**redirect_uri**，**并向url参数中追加临时授权码code及state参数**。

##### 由于当初我写的时候项目经理注册的url地址是测试环境的地址，当location.href重定向打开的时候会跳到这个地址，并在后面追加code，而我在本地拿这个code的时候尝试了很多方法，也看了很多博客但没有找到答案，最后通过后台二次重定向，将页面重定向到当前的登录页（或者再写一个页面中转也可以）并将code码拿到就可以了

```javascript
mounted(){
	 let code = this.getUrlParam(this.$route.query.redirect,'code');
	 因为我拿到是一个字符串，所以给它进行处理一下拿到code码就可以了，到时候再根据你们的实际需求去进行下一步操作。
}

methods:{
  //获取字符串中的参数
	getUrlParam(urls, name) {
                var pattern = new RegExp("[?&]" + name + "\=([^&]+)", "g");
                var matcher = pattern.exec(urls);
                var items = null;
                if (matcher != null) {
                    try {
                        items = decodeURIComponent(decodeURIComponent(matcher[1]));
                    } catch (e) {
                        try {
                            items = decodeURIComponent(matcher[1]);
                        } catch (e) {
                            items = matcher[1];
                        }
                    }
                }
                return items;
            },
}
```
