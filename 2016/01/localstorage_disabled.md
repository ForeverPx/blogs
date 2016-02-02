#localStorage在无痕模式下被禁用的问题

在移动web开发中，经常会使用到localStorage去缓存一些数据，一般情况下，我们只需要按照下面的代码去使用就不会有
问题。

```js
if(window.localStorage){
    localStorage.setItem('key','value');
}
```

但只是这样判断是不够的，现在的一些浏览器有一种功能叫*无痕浏览*，顾名思义，就是用户在浏览网页的过程中什么都不缓存，
什么也不记录，不留下痕迹。

自然地，localStorage也被禁用了。但此时window.localStorage并不是null，它还是原来的localStorage对象，只不过
这个对象的方法被禁用了。如果单纯只按上面的方法判断，js会抛出异常。

所以得用下面的代码去判断这个localStorage是否可用

```js
var storageTest = function(storage){
    if(!!storage){
        try {
            storage.setItem("key", "value");
            storage.removeItem("key");
            return true;
        } catch(e){
            return false;
        }
    }else{
        return false;
    }
}

storageTest(window.localStorage);
storageTest(window.sessionStorage);
```

原文出处：[http://www.foreverpx.cn](http://www.foreverpx.cn)
转载请注明出处。
