#让浏览器记住ajax请求并能前进和后退方法（一）

在我们浏览不同网页的时候，我们可以通过浏览器的前进、后退键来去到我们前后访问过的页面。这都有一个共同点，就是
浏览器地址栏的地址改变了。浏览器自身维护了一个记录用户访问页面历史的栈，栈中记录了用户访问不同页面的先后顺序。
但是在开发中，我们经常会用到ajax技术去提升网页的用户体验。但是ajax本身并不改变浏览器地址栏中的url，是在同一个
网页内部操作的，这时，浏览器并不会记录ajax请求的记录。在这种情况下，用户在一个页面触发的5次ajax请求后，点了后
退按钮，浏览器不会再次请求之前的ajax请求，而是返回了上一页。

解决这个问题的第一种方法就是利用location的hash值。当url的hash值改变时，页面并不会跳转，但是浏览器此时会将此
带hash的url记录到历史记录中。利用这个特性，我们可以人为的模拟带历史记录功能的ajax请求。下面是这种方法的demo。

```css
ul{
    margin:0;
    padding:0;
}

li{
    list-style: none;
    display: block;
    float: left;
    border: 1px solid #000;
    padding: 10px;
    margin-right: 20px;
    cursor: pointer;
}

li.active{
    background-color: #000000;
    color: #fff;
}
```

```html
<ul>
    <li data-id="1">1</li>
    <li data-id="2">2</li>
</ul>
```

先创建两个按钮，点击按钮时，向服务器发送请求，并将data-id通过参数带到服务器，服务器返回对应data-id的结果。
于此同时，改变按钮状态，并将location的hash值改为data-id的值。最后监听location的hash值变化，重复上述步骤。

```js
function sendAjax(hash){
    console.log("recived data:" + hash);
}

function btnStatus(hash){
    $("li").removeClass('active');
    $("li[data-id="+hash+"]").addClass('active');
}

function onHashChange(){
    var curHash = window.location.hash.replace("#","");
    if(curHash){
        btnStatus(curHash);
        sendAjax(curHash);
    }
}

window.onhashchange = onHashChange;

$("li").click(function(){
    var id = $(this).attr('data-id');
    window.location.hash = id;
});
```

当我们点击按“1-2-1”这样的顺序点击按钮时，控制台的输出如下

```bash
recived data:1
recived data:2
recived data:2
```

此时我们连续按三次返回按钮，控制台输出如下

```bash
recived data:1
recived data:2
recived data:1
```

可见这样就模拟实现了浏览器记录ajax请求的功能。

原文出处：[http://www.foreverpx.cn](http://www.foreverpx.cn)
转载请注明出处。