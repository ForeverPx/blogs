#Highcharts使用phantomjs导出图片

##描述
在用Highcharts这个强大的图标组件时，你一定在某些时刻想把画出来的图标导出为一个图片并下载到本地。Highcharts本身提供了导出功能，只需要导入exporting.js文件，就可以在图表右上角找到导出按钮了。这已经基本满足的大多数情况我们的需求。但是这原始到导出是发送请求到Highcharts的服务器上进行转化的，很多时候我们并不想把我们的数据信息暴露给他们，所以我们必须在我们自己的服务器上完成转化的服务。

##步骤一：phantomjs
Highcharts的下载文件中有个exporting-server文件夹，里面是Highcharts提供的一系列转换方案，有java，phantomjs和php的。在这里，只讲述nodejs+phantomjs的方案。

#什么是phantomjs？
phantomjs可以看做是一个不带gui的浏览器，他的内核也是webkit，我们一般用它来加载网页去解析dom并获取数据，或者是利用他来对网页进行截图。下载完phantomjs并解压后，将exporting-server文件夹中的phantomjs文件夹拷贝到解压后的根目录下。并执行命令
```bash
phantomjs ./phantomjs/highcharts-convert.js -host 127.0.0.1 -port 3003
```
执行上面的代码后，会开启一个服务器并监听3003端口。这个服务器用来接收我们发送的图表数据，并最终将生成的图片路径放回给我们。

##步骤二：获取svg数据并发送给nodejs Server端
浏览器端代码：
```js
var obj = {
    infile:$('#container').highcharts().getSVG(),
    constr:"Chart", //图表类型
    outfile:"E://chart.png" //需要存储的文件路径
};
$.ajax({
    url: "http://localhost:3000/highchartsConvert",
    type: 'POST',
    dataType: 'json',
    data: {
        data:JSON.stringify(obj)
    }
}).done(function(rData){
   download(rData);  //rData为返回的图片路径，如果转换成功的话，跟outfile的值一样
   //download方法为将文件名或路径传递到后台，然后nodejs实现文件下载功能，这里省略
});
```

nodejs端:
```js
app.post("/highchartsConvert",function(req,res,next){
    var reqData = JSON.parse(req.body.data);
    
    //发送到phantomjs转换服务器
    var options = {
        hostname: "localhost",
        port: "3001",
        path:"",
        method: "POST",
        headers: {
            "Content-Type": 'application/json;charset=utf-8',
            "Content-Length":reqData.length
        }
    };
    var req = http.request(options,function(ress){
        ress.on('data',function(data){
            res.send(data);
        });
    });
    req.write(reqData+"\n");
    req.end();
});
```

##总结
整个转换过程走了两个服务器，首先将图表数据发送到nodejs端，然后再转发到phantomjs的服务器。中间用node中转一下是因为要进行某些验证。
下面是可以传的参数列表：

infile：图表的svg代码或者options
outfile：输出图片的路径+文件名
constr：图表类型
width：图表在phantomjs中渲染的宽度
height：图表在phantomjs中渲染的高度
callback：图表在phantomjs中渲染完成后执行
更多信息请参考
RENDER CHARTS ON THE SERVER
IMPROVED IMAGE EXPORT WITH PHANTOMJS

文章作者：foreverpx