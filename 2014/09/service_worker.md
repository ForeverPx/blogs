#初识ServiceWorker

在8月份的时候，W3C更新了一个叫Service Workers的API。
了解过HTML5中的Web Worker的人可能会对这个API会更容易理解一些，不然的话会将这两个API搞混。
其实，Service Worker是基于Web Worker的事件驱动的，他们执行的机制都是新开一个线程去处理一些额外的，以前不能直接处理的任务。对于Web Worker，我们可以使用它来进行复杂的计算，因为它并不阻塞浏览器主线程的渲染。而Service Worker，我们可以用它来进行本地缓存，相当于一个本地的proxy。说起缓存，我们会想起我们常用的一些缓存技术来缓存我们的静态资源，但是老的方式是不支持调试的，灵活性不高。使用Service Worker来进行缓存，我们可以用javascript代码来拦截浏览器的http请求，并设置缓存的文件，直接返回，不经过web服务器，然后，做更多你想做的事情。
因此，我们可以开发基于浏览器的离线应用。这使得我们的web应用减少对网络的依赖。例如，我们开发了一个新闻阅读的web应用，当你用手机浏览器在有网络的情况下打开时，你可以正常的获取新闻内容。可是，如果手机进入飞行模式，那这个应用你就没法使用了。
如果我们使用了Service Worker做缓存，浏览器http请求会先经过Service Worker，通过url mapping去匹配，如果匹配到了，则使用缓存数据，如果匹配失败，则继续执行你指定的动作。一般情况下，匹配失败则让页面显示“网页无法打开”。
上面是Service Worker的基本使用场景，当然，不仅仅局限于数据缓存。
下面来看一个简单的示例：
/index.html
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <script>
        // scope defaults to "/*"
        navigator.serviceWorker.register("/service-worker.js").then(function(serviceWorker) {
            console.log("success!");
            // To use the serviceWorker immediately,
            // you might call window.location.reload()
        });
    </script>
  </head>
  <body>
  </body>
</html>
```

在chrome中的console中输入navigator.serviceWorker你会发现返回的undefined。这可能是因为你chrome的版本小于36，或者你没有打开这个功能。所以先确保你的chrome升级到了36以上。然后进入chrome://flags/中将ServiceWorker开关打开，如下图：
ServiceWorker
确保以上两个步骤都完成后，就可以在控制台看到返回值：
ServiceWorker
上面的代码中，给serviceWorker注册了一个叫service-worker.js的文件，这里采用的promise写法。当注册成功后，会执行then里面的回调函数。
下面看看service-worker.js
```js
this.oninstall = function(e) {
    // Create a cache of resources.
    var resources = new Cache();
    var visited = new Cache();
    // Fetch them.
    e.waitUntil(resources.add(
        "/index.html",
        "/fallback.html",
        "/css/base.css",
        "/js/app.js",
        "/img/logo.png"
    ).then(function() {
        // Add caches to the global caches.
        return Promise.all([
            caches.set("v1", resources),
            caches.set("visited", visited)
        ]);
    }));
};
 
this.onfetch = function(e) {
    e.respondWith(
        // Check to see if request is found in cache
        caches.match(e.request).catch(function() {
            // It's not? Prime the cache and return the response.
            return caches.get("visited").then(function(visited) {
                return fetch(e.request).then(function(response) {
                    visited.put(e.request, response);
                    // Don't bother waiting, respond already.
                    return response;
                });
            });
        }).catch(function() {
            // Connection is down? Simply fallback to a pre-cached page.
            return caches.match("/fallback.html");
        });
    );
};
```

- oninstall：当worker装载完毕时执行。在oninstall的回调函数中，将一些需要缓存的静态的文件写入全局的cache对象中。
- onfetch：当接收到用户的请求时执行

通过e.request去匹配缓存中的静态文件，如果匹配到，则将这个缓存文件返回，如果未匹配到，则被catch获取，将请求释放（去请求真正的服务器），并将这个新文件缓存到全局cache。这里也是用到了promise写法。
在我看来，用ServiceWorker做缓存就相当于一个proxy，可以拦截http请求，这样你可以对请求做一下定制化的需求。即使在无网络的情况下，ServiceWorker也可以被当做一个http服务器一样返回前端所需要的数据。
上面是我对ServiceWorker的一些参考和理解。目前相关的中英文资料还比较少，主要还是得看W3C的介绍。

参考资料：
- [ServiceWorker简单例子](http://www.serviceworker.org/#)
- [Github上一些代码](https://github.com/w3c-webmob/ServiceWorkersDemos)
- [W3C ServiceWorkers](http://www.w3.org/TR/2014/WD-service-workers-20140508/#introduction)

文章作者：foreverpx
