#IE8下元素背景为透明时，hover事件失效的bug

在IE8下，当一个div设置了背景为透明或者不设置任何背景时，在该div上注册的hover事件是不会触发的。

```html
<div class='outer'>
    <div class='inner'>
        此处有文字
    </div>
</div>
```

```css
.outer{
    width:100px;
    height:100px;
}

.outer:hover{
    color:#000000;
}
```

上面这段代码中，给outer加上了hover的伪类，正常情况下，鼠标移到outer的100*100的区域中，就会触发hover，并使得内部的文字的颜色
变成黑色。但是在IE8下面，鼠标在除文字以往的区域都不回触发hover，只有在鼠标位于文字上时，才会触发。

针对于这个bug，有如下解决方案：

1.给outer设置真实的背景图片，不过是一张1*1的透明png。

```css
.outer{
    background-image:url('1.png');
}
```

2.给outer设置一个不存在的图片，不过会产生404请求。
```css
.outer{
    background-image:url('.');
}
```

更多的参考资料可看如下链接：

[http://blog.rednael.com/2009/09/01/TrappingMouseEventsOnTransparentInputElementsOrTextareasInIE.aspx](http://blog.rednael.com/2009/09/01/TrappingMouseEventsOnTransparentInputElementsOrTextareasInIE.aspx)