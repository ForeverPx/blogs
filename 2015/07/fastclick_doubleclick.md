#Fastclick 导致click事件触发两次的问题

我在移动web上使用Fastclick这个库去解决300ms延迟问题，但是在安卓4.2下的webview中引发了另一个比较奇怪的bug。

在A页面中有个 a button，在B页面中有个 b button，a和b都在同一个position，给a和b都注册一个click事件。a的click事件触发后跳转到B页面。当a被点击后调到B页面，你会发现b按钮的click事件也被触发了。

没错，事件‘穿透’了两个页面！

但其实并没有穿透，点击a按钮时，其实有如下两个动作：

 1. fastclick用touchstart等事件模拟的click事件的触发，只是第一次click。
 2. 设备原本的300ms延迟后的click事件。此时页面已加载成B，而这个click点击的是B页面上的b按钮。
 
在ios等设备中，第二个click事件其实被fastclick屏蔽掉了，所以没出现这个问题，但是在android 4.2的浏览器中却bug搬的屏蔽失效，导致这个问题。

查了许多的相关资料，老外称这个现象为`ghost click`，大部分并没有什么有效的解决方案。

最后在stackoverflow中找到了一个hack方案，如下：

```css
preventClick { width:100%; height:100%; position:absolute; z-index:1000; top:0; left:0; }
```

```html
<body>
    <div id="preventClick"></div>
</body>
```

```js
function onDeviceReady() {
    setTimeout(function(){ $('#preventClick').hide(); }, 300);
}
```

原理是每个页面前300ms都被一个透明的div去覆盖，所以第二个click是点不到对应的button。。

虽然这种方式比较暴力，但是还是奏效的，在没什么更好的方式前，可以先hack之。

文章原文链接：[https://github.com/ForeverPx/blogs/blob/master/2015/07/fastclick_doubleclick.md](https://github.com/ForeverPx/blogs/blob/master/2015/07/fastclick_doubleclick.md)
转载请注明出处。


