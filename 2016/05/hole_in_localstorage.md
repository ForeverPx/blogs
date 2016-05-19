#localstorage使用不严谨之坑

今天上线新版本后，发现极个别“老”用户在微信浏览器中无法打开我们网站的首页。在经过一番线上文件代理替
换后，终于发现了问题所在。

问题代码段：
```js
if(localstorage.getItem("things")){
    var things = localstorage.getItem("things");

    use(things);

    //缓存用一次就删除
    localstorage.removeItem('things');
}else{
    use(newData);
}

```

这段代码乍一看没什么问题，但是有隐患。在老版本中，localstorage中存的things内容如下：

```js
{
    name:'px',
    age:'25'
}
```

但是到了新版本，由于需求问题，这个缓存的值改变了，变成了如下结构：

```js
{
    username:'px',
    myage:'25'
}
```

这样就导致了在使用*use*函数处理*things*的时候报错了，导致后面的removeItem永远不执行，所以缓存
的这段数据在代码中永远不被清除，use函数一直使用旧的数据进行渲染，这样就一直报错，永远无法使用新
数据。

这里有两点需要改进的
* 给缓存加版本号
* 用变量读取缓存后，立刻清除缓存

优化后的代码如下：

```js
//先判断缓存版本号
if(localstorage.getItem("version") == curVersion){
    if(localstorage.getItem("things")){

        var things = localstorage.getItem("things");
        //立刻清除
        localstorage.removeItem('things');

        use(things);
    }else{
        use(newData);
    }
}else{
    localstorage.removeItem('things');
    use(newData);
}
```
