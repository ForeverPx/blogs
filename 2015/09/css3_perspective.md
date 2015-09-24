#[翻译]CSS3 perspective 用法

CSS3 perspective属性让元素拥有了视野和视角的效果，而不是像以往的贴在屏幕上的平面，模拟了眼睛与物体之前的距离带来的远近视差效果。比如你站在铁轨上，往铁轨的方向看去，你会看到铁轨并不是平行的，而是趋于相交的。

它值的大小决定了眼睛与元素的距离，值越小则离得越近，值越大则离的越远。同样在页面上的两个2D正方形，如果perspective属性的值不同，则大小就不一样。

需要注意的是，perspective并不影响当前元素的渲染，而是影响它的所有子元素。这也是它跟*transform:perspective()*方法的主要区别，例子如下。

```html
<div class="parent perspective">
    <h1>Using perspective property on parent</h1>
    <div class="child"></div>
    <!--......-->
</div>

<div class="parent transform">
    <h1>Using transform: perspective() on children</h1>
    <div class="child"></div>
    <!--......-->
</div>
```

```css
.parent.perspective {
  perspective: 50em;
}

.parent.perspective .child {
  transform: rotateX(50deg);
  background: tomato;
}

.parent.transform .child {
  transform: perspective(50em) rotateX(50deg);
  background: deepskyblue;
}
```
下图左边为perspective属性的效果，右边为transform:perspective()的效果

![](http://i3.tietuku.com/b6d4cc479d691203.png)

让我们看一个更cool的例子

```html
<div class="wrapper w1">
  <h1><code>perspective: 1000px</code></h1>
  <div class="cube">
    <div class="side  front">1</div>
    <div class="side   back">6</div>
    <div class="side  right">4</div>
    <div class="side   left">3</div>
    <div class="side    top">5</div>
    <div class="side bottom">2</div>
  </div>
</div>
<div class="wrapper w2">
  <h1><code>perspective: 250px</code></h1>
  <div class="cube">
    <div class="side  front">1</div>
    <div class="side   back">6</div>
    <div class="side  right">4</div>
    <div class="side   left">3</div>
    <div class="side    top">5</div>
    <div class="side bottom">2</div>
  </div>
</div>
```

```css
.wrapper {
  width: 50%;
  float: left;
}

.w1 {
  perspective: 1000px;
}

.w2 {
  perspective: 250px;
}

.wrapper h1 {
  text-align: center;
}

.cube {
  font-size: 4em;
  width: 2em;
  margin: 1.5em auto;
  transform-style: preserve-3d;
  transform: rotateX(-40deg) rotateY(32deg);
}

.side {
  position: absolute;
  width: 2em;
  height: 2em;
  
  background: rgba(tomato, .6);
  border: 1px solid rgba(0,0,0,.5);
  
  color: white; 
  text-align: center;
  line-height: 2em;
}

.front  { transform:                  translateZ(1em); }
.top    { transform: rotateX( 90deg)  translateZ(1em); }
.right  { transform: rotateY( 90deg)  translateZ(1em); }
.left   { transform: rotateY(-90deg)  translateZ(1em); }
.bottom { transform: rotateX(-90deg)  translateZ(1em); }
.back   { transform: rotateY(-180deg) translateZ(1em); }
```

效果如下图：

![](http://i3.tietuku.com/40e525e00e9ca7e7.png)

