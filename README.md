weixin-api
==========

微信公众API For Node.js

说明
===========

此package是针对微信公众平台[消息接口](http://mp.weixin.qq.com/wiki/index.php?title=%E6%B6%88%E6%81%AF%E6%8E%A5%E5%8F%A3%E6%8C%87%E5%8D%97)的实现。

安装
===========

```bash
  npm install weixin-api
```

使用
===========
使用express开发微信公众平台应用

首先安装express
```bash
	npm install express -g
```

创建应用
```bash
	express myweixin
```

`cd myweixin`修改`package.json`，添加对`weixin-api`的依赖

```javascript
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "3.1.1",
    "jade": "*",
	"xml2js" : "0.2.6",
	"weixin-api" : "*"
  }
}
```

然后执行
```bash
	npm install
```

实例
===========

```javascript
var express = require('express'),
	weixin = require('weixin-api'),
	app = express();

// 解析器
app.use(express.bodyParser());
//app.use(xmlBodyParser);

// 接入验证
app.get('/', function(req, res) {
		
	// 签名成功
	if (weixin.checkSignature(req)) {
		res.send(200, req.query.echostr);
	} else {
		res.send(200, 'fail');
	}
});

// config
weixin.token = 'fe5d94644bc4360aa3313886c8';

// 监听文本消息
weixin.textMsg(function(msg) {
	console.log("textMsg received");
	console.log(JSON.stringify(msg));

	var resMsg = {};

	switch (msg.content) {
		case "文本" :
			resMsg = {
				fromUserName : msg.toUserName,
				toUserName : msg.fromUserName,
				msgType : "text",
				content : "(╯°□°）╯︵ ┻━┻",
				funcFlag : 0
			};
			break;
	
		case "音乐" :
			resMsg = {
				fromUserName : msg.toUserName,
				toUserName : msg.fromUserName,
				msgType : "music",
				title : "向往",
				description : "李健《向往》",
				musicUrl : "",
				HQMusicUrl : "",
				funcFlag : 0
			};
			break;
		
		case "图文" :
		
			var articles = [];
			articles[0] = {
				title : "PHP依赖管理工具Composer入门",
				description : "PHP依赖管理工具Composer入门",
				picUrl : "http://weizhifeng.net/images/tech/composer.png",
				url : "http://weizhifeng.net/manage-php-dependency-with-composer.html"
			};

			articles[1] = {
				title : "八月西湖",
				description : "八月西湖",
				picUrl : "http://weizhifeng.net/images/poem/bayuexihu.jpg",
				url : "http://weizhifeng.net/bayuexihu.html"
			};

			articles[2] = {
				title : "「翻译」Redis协议",
				description : "「翻译」Redis协议",
				picUrl : "http://weizhifeng.net/images/tech/redis.png",
				url : "http://weizhifeng.net/redis-protocol.html"
			};
	
			// 返回图文消息
			resMsg = {
				fromUserName : msg.toUserName,
				toUserName : msg.fromUserName,
				msgType : "news",
				articles : articles,
				funcFlag : 0
			}
	}

	weixin.sendMsg(resMsg);
});

// 监听图片消息
weixin.imageMsg(function(msg) {
	//
	console.log("imageMsg received");
	console.log(JSON.stringify(msg));
});

// 监听位置消息
weixin.locationMsg(function(msg) {
	//
	console.log("locationMsg received");
	console.log(JSON.stringify(msg));
});

// 监听链接消息
weixin.urlMsg(function(msg) {
	//
	console.log("urlMsg received");
	console.log(JSON.stringify(msg));
});

// Start
app.post('/', function(req, res) {
	
	// loop
	weixin.loop(req, res);

});

app.listen(3000);
```

