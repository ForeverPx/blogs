#​Express文件上传之Multer

Multer是一个nodejs中间件，用来处理http提交multipart/form-data，也就是文件上传。它是在busboy的基础上开发的。
在我看来，Multer是众多上传中间件里面使用起来最优雅的，且能满足大部分的上传要求。API也相对来说较为直观和简单。

##安装
```js
npm install multer --save
```

基本用法

```js
var express = require('express')
var multer  = require('multer')
var app = express()
app.use(multer({ dest: './uploads/'}))

router.use(function(req,res,next){
	console.log(req.files); //JSON Object
	next();
});
```

从上面的代码可以看到，multer作为一个中间件传入了app.use，当有上传请求到来时，express会拦截此请求并通过multer组件完成上传操作。在multer初始化方法中传入的是配置对象，我们可以在里面配置我们自定义的参数，例如“文件大小限制”，“文件数量限制”等等。
不仅可以添加限制，还能给上传注册事件，例如：
```js
//上传开始时触发
onFileUploadStart:function(file){
	console.log("upload start");
}
//上传结束时触发
onFileUploadComplete:function(file){
	console.log("upload complete");
}
......
```

我们在真正接受上传的action中，通过req中的files获得文件的基本信息，此信息为JSON格式，常用的信息如下。
- originalname - 文件原名
- path - 上传文件存放的路径
- size - 文件大小
- 更多详细的属性和配置请参见Multer的Github主页。

文章作者：foreverpx