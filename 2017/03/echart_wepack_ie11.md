# Echart在使用webpack打包之后，IE10+中无法渲染的问题

今天在IE11上开发时，发现Echart在IE11上无法渲染出来，控制台报错  SCRIPT65535: Invalid calling object

错误指向的是Echart依赖的Zrender模块的requestAnimationFrame模块，指示requestAnimationFrame方法调用失败

查阅MSDN关于IE兼容差异的文档，发现有一篇叫做 [函数指针方法调用](https://msdn.microsoft.com/library/gg622930(v=vs.85).aspx) 的文章

文章大意如下：

`早期版本的 Windows Internet Explorer 支持将方法的指针进行缓存并随后使用缓存的指针来调用方法。自 Windows Internet Explorer 9 开始，取消了这项支持以改善与其他浏览器的互操作性。`

```js
var d = document.writeln;
d("<script language=VBScript>"); //error Invalid calling object
```

`从 Internet Explorer 9 开始，需要有一个对象才能调用方法。默认情况下，在全局作用域中提供 window 对象（如前面示例中所示）。不过，window 对象没有 writeln 方法，因此报告 JavaScript 错误消息：“调用对象无效”。`

`解决这个报错的方案是显示提供适当的调用对象，或使用 JavaScript 的 bind API 将隐式调用对象与该方法关联。`

```js
d.call(document, '<script language="VBScript">');

or

var d = document.writeln.bind(document);
d("<script language=VBScript>"); // Now this is OK.
```

根据这个原理，我们稍微修改下requestAnimationFrame模块

```js
//requestAnimationFrame.js 模块
module.exports = (typeof window !== 'undefined' &&
                                (window.requestAnimationFrame
                                || window.msRequestAnimationFrame
                                || window.mozRequestAnimationFrame
                                || window.webkitRequestAnimationFrame))
                            || function (func) {
                                setTimeout(func, 16);
                            };

```

修改为

```js
//requestAnimationFrame.js 模块
module.exports = (typeof window !== 'undefined' &&
                                (window.requestAnimationFrame
                                || window.msRequestAnimationFrame.bind(window)
                                || window.mozRequestAnimationFrame
                                || window.webkitRequestAnimationFrame))
                            || function (func) {
                                setTimeout(func, 16);
                            };

```

即可解决。

不过，解决这个问题还有另一个方法。

在webpack的配置中，我们在开发环境使用

```js
devtool: 'eval-source-map'
```

来定位错误，去掉eval方式亦可解决此问题，不过原因不详，还有待探究。
