#跨域Ajax之ContentType:application/json

在使用Ajax跨域请求时，如果设置Header的ContentType为application/json,会分两次发送请求。第
一次先发送Method为OPTIONS的请求到服务器，这个请求会询问服务器支持哪些请求方法（GET,POST等），
支持哪些请求头等等服务器的支持情况。等到这个请求返回后，如果原来我们准备发送的请求符合服务器的规
则，那么才会继续发送第二个请求，否则会在Console中报错。

为什么在跨域的时候设置ContentType为application/json时会请求两次？其实JQuery的文档对此有做
说明。

**contentType (default: 'application/x-www-form-urlencoded; charset=UTF-8')**

Type: Boolean or String

When sending data to the server, use this content type. Default is "application/x-www-form-urlencoded; charset=UTF-8", which is fine for most cases. If you explicitly pass in a content-type to $.ajax(), then it is always sent to the server (even if no data is sent). As of jQuery 1.6 you can pass false to tell jQuery to not set any content type header. Note: The W3C XMLHttpRequest specification dictates that the charset is always UTF-8; specifying another charset will not force the browser to change the encoding. **Note: For cross-domain requests, setting the content type to anything other than application/x-www-form-urlencoded, multipart/form-data, or text/plain will trigger the browser to send a preflight OPTIONS request to the server.**

注意Note后面的描述，在跨域的时候，除了contentType为application/x-www-form-urlencoded, multipart/form-data或者text/plain外，都会触发浏览器先发送方法为OPTIONS的请求。

比如说，你原来的请求是方法方法POST，如果第一个请求返回的结果Header中的Allow属性并没有POST方法，
那么第二个请求是不会发送的，此时浏览器控制台会报错，告诉你POST方法并不被服务器支持。

下面看下OPTIONS请求的返回图

![](http://o929v8ilh.bkt.clouddn.com/045DA4EC-7C23-494B-B0D7-BC681AA7A37A_meitu_1.jpg)

图中箭头指向的Allow就是服务器返回的支持的方法。

不仅如此，如果想要用ContentType:application/json发送跨域请求，服务器端还必须设置一个名为
Access-Control-Allow-Headers 的Header，将它的值设置为 Content-Type，表明服务器能够接收
到前端发送的请求中的ContentType属性并使用它的值。否则第二次请求也是发不出去的，浏览器console会
报错，并提示你服务器没有设置Access-Control-Allow-Headers。
