# HTML5 input file 打开很慢的问题

开发中遇到了这样一个问题

我们在代码中使用了HTML5的input[file]标签去上传图片

```html
<input type="file" name="file" class="element" accept="image/*">
```

同时，在上面代码中，使用了 accept="image/*" 去过滤所有非图片的文件。

但是，在最新版本的chrome（52.0.2743.116 (64-bit)）中，出现了一个很奇特的问题。

点击input之后，会有一定概率出现文件选择框弹出非常慢的问题，正常情况下，不到1S就能弹出文件选择框。但是慢的时候，可能达到7 ~ 10秒。

对属性进行逐一排查后，发现是accept="image/*"的问题。

将accept="image/*"改为指定的图片格式就不会出现上述的概率性问题，所以我将上传图片的过滤格式指定为了常用的几种格式

```html
<input type="file" name="file" class="element" accept="image/jpg,image/jpeg,image/png">
```

当然，如果希望过滤所有的非图片格式，那么这个问题还是会存在。原因初步猜想是当设置accept="image/*"时，浏览器会在弹出框中处理所有的非图片元素，包含所有的图片格式，如果文件较多会增加处理时间，而这个时候可能在这个版本的chrome中有bug（也许是底层没实现好），导致概率性时间增长。
