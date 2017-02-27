# Zepto在跨域时无法带上cookie的bug

在A域的代码中，用zepto的ajax方法去向B域请求数据。同时，我们希望把B域的cookie也随请求带过去，代码如下

```js
$.ajax({
    url: 'B',
    type: 'GET',
    xhrFields: {
        withCredentials: true
    }
});
```

withCredentials这个参数是为了让XMLHttpRequest发起跨域请求的时候，能带上目标域的cookie。

这在使用jquery的时候并没有问题，但是使用zepto的时候，发现cookie并没有被带上。

在官方文档中，关于ajax的部分是这样写的

```json

xhrFields (default: none): an object containing properties to be copied over verbatim to the XMLHttpRequest instance. v1.1+

```

意思是可以在options里面设置xhrFields的相关属性，比如上面提到的withCredentials。值得注意的是，后面标注了实现版本是1.1+。我们目前使用的是1.1.1，按理说应该是支持的。

在反复尝试后，cookie依然无法带过去，所以只能通过看源码解决了。确认下是否真的实现了这个属性。

下面是1.1-stable的版本：

[zepto-ajax-1.1-stable](https://github.com/madrobby/zepto/blob/1.1-stable/src/ajax.js)

在代码中，搜索xhrFields，发现只有如下一处与之相关

```js

 if (settings.xhrFields) for (name in settings.xhrFields) xhr[name] = settings.xhrFields[name]

```

此处xhrField是通过setting对象带进来的，而setting对象是在ajaxBeforeSend方法中传入的，所以跟ajax初始化时的options参数并没有关系。

所以得出的结论是zepto库的ajax模块中，并没有实现与文档相匹配的options的xhrFields属性，如果要达到带cookie的效果，只能通过下面代码解决

```js
    $.ajaxSettings.beforeSend = function(xhr) {
        // see https://github.com/madrobby/zepto/issues/274
        xhr.withCredentials = true;  // TODO(elsigh): Do this in zepto w/ xhrFields.
    };
```

顺便查看了一下1.2.0版本ajax模块的代码，也没有实现通过options设置xhrFields的功能。。。