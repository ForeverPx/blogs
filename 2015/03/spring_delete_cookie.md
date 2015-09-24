Java中Spring删除cookie

web开发中，在controller上需要将浏览器的某个cookie删除，我们会期望response参数里面会有个removeCookie方法，但悲剧的是没有。response里面只有addCookie方法。

如果我们想删除cookie，只能利用addCookie方法将其删除。
假如我们要删除一个叫sessionId的cookie，代码如下:

```java
Cookie cookie = new Cookie("sessionId",null);
cookie.setMaxAge(0);
cookie.setPath("/");
response.addCookie(cookie);
```
执行如上代码就能将某个cookie删除。
需要注意的是，cookie的path一定要和你想要删除的cookie的path匹配，不然只会把sessionId的值设置为空，而不会删除该cookie。

文章作者：foreverpx