#移动web开发之——iso中的video

移动web开发中，我们常常会用到video标签去播放多媒体视频。但不同于pc端浏览器中使用video标签，
很多属性和方法在ios中的safari是被限制的，同时也新增了一些特性，下面一一道来。

##对于小屏幕的优化
在iphone这样的小屏幕中，当用户点击播放按钮时，总是以全屏的方式去播放视频，此时video会适配横屏
和竖屏模式。由于总是以全屏播放，你会发现设置control属性之后，在video标签加载完毕后是看不到控制栏的
，只有video的一张post在页面中，点击post全屏播放视频后，control才会出现。我觉得这样设定是为了
能让用户具有更好的观看体验，屏幕本来就小，在如此小的屏幕下还不全屏看就太难受了。但这个设定只是针对
ios中的safari，而不针对MacOs中的safari。  

##移动网络下的用户操作
用户使用手机的时候很有可能是在移动2-4G的网络情况下，所以在ios的Safari中，video是不允许预加载和
自动播放的。只有当用户手动触发开始事件之后，诸如`play()`、`stop()`这样的方法才能起作用，例如
```html
    <input type="button" value="Play" onclick="document.myMovie.play()">
    <body onload="document.myMovie.play()">
```
像上面这段代码，在ios的safari中就不起作用。只有当用户亲自触发开始时，才能用play()函数去控制
video。这样设定的目的是为了防止用户产生过高的流量费用，因为用户打开网页时，可能并没有打算去观看
视频。PS: audio标签也是同样的规则。

##ios中video的默认高度和宽度
ios中，当没有指定高和宽时，video的默认高度和宽度为150*300，当video开始播放后，高和宽是不能
改变的了。

##支持的格式
* desktop: Safari中，media能支持的视频格式取决于系统中安装的QuickTime。

* iphone: Safari中，media支持无损的WAV、AIF、MP3、HE-ACC、MPEG-4、H264。

##一个页面上有多个video或audio实例
现在ios中不支持一个页面同时播放两个（包含两个）以上的video或audio。

##音量控制
* desktop: Safari可以通过volume属性去设置media的音量，但是这个音量的范围是跟系统相关的。当设置的
值为1时，音量为当前用户设置的系统音量。当设置为0时，音量为静音。其他的音量的0~1之间。

* iphone: 在ios中，音量只受用户通过物理按键去控制，通过javascript去设置volume属性是无效的。读取volume属性会
永远返回1。

##播放速率
* desktop: 可以设置0~1的值给playbackRate属性去改变播放速率。

* iphone: 不支持playbackRate属性。

更多关于safari中的媒体标签的细节请参考苹果的官网文档：
[https://developer.apple.com/library/safari/documentation/AudioVideo/Conceptual/Using_HTML5_Audio_Video/Device-SpecificConsiderations/Device-SpecificConsiderations.html#//apple_ref/doc/uid/TP40009523-CH5-SW1](https://developer.apple.com/library/safari/documentation/AudioVideo/Conceptual/Using_HTML5_Audio_Video/Device-SpecificConsiderations/Device-SpecificConsiderations.html#//apple_ref/doc/uid/TP40009523-CH5-SW1)

原文出处：[http://www.foreverpx.cn](http://www.foreverpx.cn)
转载请注明出处。
