#CSS3 display:box 用法

css3 给display属性带来了box这个值，这个新值给开发带来了极大的方便。

其中的一个场景如下：

```html
<div id='outer'>
	<div id='a1'></div>
	<div id='a2'></div>
	<div id='a3'></div>
</div>
```

假如我们想要使得内部的123元素水平以不同的比例分割outer所占的空间，以往的做法我们可能会让123元素浮动或者设置display属性为inline-block，然后再给width属性设置宽度百分比去分割outer。

使用display:box后可以这样实现：

```css
#outer{
	width:600px;
    height:100px;
    display:-moz-box;
    display:-webkit-box;
    display:box;
}
#a1{
    background:blue;
    -moz-box-flex:1;
    -webkit-box-flex:1;
    box-flex:1;
}
#a2{
    background:green;
    -moz-box-flex:2;
    -webkit-box-flex:2;
    box-flex:2;
}
#a3{
    background:yellow;
    -moz-box-flex:3;
    -webkit-box-flex:3;
    box-flex:3;
}
```
效果如下：
![none](http://img.blog.csdn.net/20150512195648399)

子元素中box-flex属性为该元素的占比，若想让此属性生效，父元素必须设置display:box。以a3为例，a3设置了box-flex为3，则他所占的宽度为**600*(3/(1+2+3))**为300px。

如果其中一个子元素设置了固定宽度100px，则剩下的500px将会参与到比例划分的规则中。

除此之外，还有几个与display:box相关的属性。

1.box-orient
horizontal | vertical | inline-axis | block-axis | inherit
用来确定子容器的排列方式（水平or垂直or。。。）

2.box-direction
normal | reverse | inherit
用来确定子容器的方向，如果设置为reverse，则刚才代码的显示效果为a3->a2->a1。

3、box-align
start | end | center | baseline | stretch
box-align表示父容器里面子容器的垂直对齐方式（顶部对齐or底部对齐or中线对齐。。。）

3、box-pack
start | end | center | justify
box-pack表示父容器里面子容器的水平对齐方式
分别代表
左对齐，右对齐，居中对齐，水平均分。

文章作者：forevercjl
转载请注明出处。









