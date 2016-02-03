#iScroll在安卓下无法点击的问题

iScroll解决安卓下无法点击在网上已经有了解决方案，代码如下：

```js
function iScrollClick(){
	if (/iPhone|iPad|iPod|Macintosh/i.test(navigator.userAgent)) return false;
	if (/Chrome/i.test(navigator.userAgent)) return (/Android/i.test(navigator.userAgent));
	if (/Silk/i.test(navigator.userAgent)) return false;
	if (/Android/i.test(navigator.userAgent)) {
	   var s=navigator.userAgent.substr(navigator.userAgent.indexOf('Android')+8,3);
	   return parseFloat(s[0]+s[3]) < 44 ? false : true
    }
}

myScroll = new IScroll("#ID", {
	click:iScrollClick()
});
```

这样看上去是没什么问题，但在实际开发中，会有一个坑：在Android4.4+的手机上，是要区分webview和chrome的，
按照上面的正则，会导致chrome下使用iscroll后无法点击。

上面的正则无法判断在Android4.4+的手机上，用户到底是用的是自带的webview还是chrome浏览器。
这两个浏览器调用userAgent所显示的信息几乎一样，不用之处只有chrome的版本号。
webview使用的版本号会比真正的chrome的版本号小很多。所以只能利用这个版本号来区分这两个浏览器。修改后的代码如下

```js
iScrollClick = function(){
    if (/iPhone|iPad|iPod|Macintosh/i.test(navigator.userAgent))
        return false;
    if (/Silk/i.test(navigator.userAgent))
        return false;
    if (/Android/i.test(navigator.userAgent)) {
        var chromeVersion = 0;
        if (/Chrome/i.test(navigator.userAgent)){
            chromeVersion = navigator.userAgent.substr(navigator.userAgent.indexOf('Chrome')+7,2);
        }
        var s=navigator.userAgent.substr(navigator.userAgent.indexOf('Android')+8,3);
        if(parseFloat(s[0]+s[3]) < 44){
            if(chromeVersion < 40){
                return false;
            }else{
                return true;
            }
        }
        return true;
    }
}
```

这里我是认为如果chrome的版本号小于40，就为webview,现在的webview版本号还停留在27。

原文出处：[http://www.foreverpx.cn](http://www.foreverpx.cn)
转载请注明出处。