#安卓微信浏览器中location.href失效的问题

在移动web中，经常会使用window.location.href去跳转页面，这个方法在绝大多数浏览器中都不会
存在问题，但是在安卓手机的微信自带浏览器中，会出现一个奇怪的bug。

```js
window.location.href = baseUrl + 'article/comment';
```

上面的代码是通过href属性赋值去跳转到文章的评论页面，这段代码在安卓手机的微信自带浏览器中会偶发性的失效，
调用上面的代码后，浏览器进度条加载完后，页面并没有跳转，还是停留在当前页面。

暂时还没找到具体的原因，但是当时遇到问题时，感觉是缓存在捣乱，所以尝试着给跳转链接后面加了一个随机数或者
时间戳，结果是问题解决了。

```js
window.location.href = baseUrl + 'article/comment?v='+(new Date().getTime());

window.location.href = baseUrl + 'article/comment?v='+Math.random();
```

原文出处：[http://www.foreverpx.cn](http://www.foreverpx.cn)
转载请注明出处。
