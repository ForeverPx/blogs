#移动web开发之——viewport
在绝大多数移动端的web页面中，head标签里面都会有这样一行代码

```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1,user-scalable=0" />
```

然而PC端的页面中却不怎么出现。那么这个`viewport`的作用是什么呢？

`viewport`根据字面翻译，为视窗，窗口。`viewport`的宽度决定了`html`这个标签的宽度。`viewport`又分为`visual viewport`(可视窗口）和`layout viewport`（布局窗口）。

可以想象如下一种场景，你做在电影院的某一排看电影，荧幕的大小肯定是固定的，此时荧幕的大小就相当于`layout viewport`。你的眼睛的视野范围就可以理解为`visual viewport`，这个范围取决于你离屏幕的距离，离荧幕越近，你看到的屏幕范围就越小，就看不全，离的越远，你看到的屏幕就更加全。

在PC端时，由于屏幕尺寸一般都比较大，所以浏览器默认viewport为clientWidth，也即是`visual viewport`=`layout viewport`=`clientWidth`。所以当你的html宽度小于clientWidth时，你能看到全部的内容，就如同屏幕刚好占满你的视野。

但是在移动端，比如手机，屏幕的尺寸一般很小，如果默认使得两个viewport值相等的话，那么几乎所有的pc页面都会乱的不成样子。所以手机浏览器中，`visual viewport`默认为手机屏幕的大小，而`layout viewport`默认为800px到1000px不等，不同浏览器的初始值不一样。当你用手机打开一个pc端的网页时，按照上面的规则，你只能看到其中的一部分，剩下的部分需要滚动屏幕才能看到。但是许多设备的浏览器为了让用户能看全页面，而选择了最小缩放，也就相当于你离荧屏更远以保证你能看到全部的屏幕。但是在电影院中，毕竟最远距离是有限的，所以这个缩放也是有范围的。当达到最小缩放的时候，如果元素的宽度（px）依然很宽（荧屏是在太大了，最后一排也看不全），那么就会出现横向滚动条（你只能摇脑袋了）。

width为100%的时候：

![1](http://i12.tietuku.com/d0b481b297a3734b.jpg)

width为2000px的时候：

![2](http://i12.tietuku.com/271bd7bb7cfa542e.jpg)

在移动端开发中，一般是不允许出现横向滚动条的，与app相同，而且得保证诸如按钮这样的元素能显示出合适的大小来让用户去触摸。所以得避免`layout viewport`太宽以及避免浏览器的缩放。这个时候我们就得用到文章开头的那句代码。

`width=device-width`表示我们将`layout viewport`的宽度设为跟设备一样宽，以IPHONE6为例，IPHONE6的横向分辨率为750，转换之后所代表的css宽度为375px，这个时候`layout viewport`的宽度就为375px，也即是html的宽度为375px。一般设计稿也是以375或者750来出图，这样就可以很方便的在设备上实现页面了。

假设在设计稿中有一个宽200px，高70px的按钮，效果如下：

![3](http://i5.tietuku.com/60f24d52c870f5c8.jpg)

如果不设置viewport，则是这个样子的：

![4](http://i5.tietuku.com/87f3db358774c8b4.jpg)

`initial-scale=1, maximum-scale=1`是将最大和最小缩放都设置为1，也即是不缩放，并且使用`user-scalable=0`禁用了用户通过双指缩放。

原文出处：[http://www.foreverpx.cn](http://www.foreverpx.cn)
转载请注明出处。



