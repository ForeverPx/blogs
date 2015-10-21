#什么是sql注入？
简单来说，sql注入就是你写的sql执行了你并不期望执行的数据查询逻辑。

下面来看一个简单的例子。有一个url请求如下：
```bash
http://www.foreverpx.cn/blogs?id=123456
```
上面这个请求的功能是通过id获得某篇blog的数据，sql注入就可以从这里开始。

我们将`id=123456`替换掉：
```bash
http://www.foreverpx.cn/blogs?id=123456 and 1=1 union select * from catalog where id=2
```
上面如果存在id为2的catalog记录，且列数相等，则会返回blogs和catalog的联合结果，但我们的本意只是想查询blogs出来。有人会疑问，
中间的`1=1`有什么用处？其实`1=1`是一个试探语句，用来试探这个请求是否有sql注入的可能。还是以上面的请求为例。

如果and后面是1=1，则如果存在id为123456的blogs，则与运算两边的表达式都为真，则返回的结果是正确的。如果我们将1=1改为1=2，则
与运算左边是真，右边是假，则整个与运算为假，所以并不返回结果或者返回错误的结果。利用这样的技巧可以判断是否有sql注入的可能。

再举一个登录的例子。如果登录的sql是下面这样的：
```bash
select * from user where username = '?' and password = '?'
```
现在模拟登录请求，将username字段赋值为`admin' or 1=1 /*`。结果就是sql直接忽略了password而直接登录了admin账户。`/*`将后面的
语句注释掉了，用用户名就可以登陆。但是这只是最简单的情况，实际情况肯定要比这复杂的多。

#获取返回字段数
sql注入还能获得当前返回的数据列数。还记得`union`查询吧？union查询执行第二条语句并把结果与第一条语句的结果合并。但这有个前提，就是
两次的结果集的列数需要一致，利用这个规则，我们可以试探出有多少列。
```bash
http://www.foreverpx.cn/blogs?id=123456 and 1=1 union select 1,2,3,...  // 从1开始，直到返回正确结果为止
```
当返回正确结果时，当前的数字就是列数。

还有一种方法是利用`order by`,what? order by 也可以？没错。
```bash
select * from user where username = '?' order by 1
```
这里利用了mysql支持列编号排序的规则。`order by 1`表示按照第一列排序，其实看到这应该懂了吧，如果你写了一个不存在的列序号，就
会报错，所以临界值就是列数。

#系统函数
`load_file`
```bash
select * from user where username = '?' union select 1,load_file(0x111111111111111),3
```
load_file传入的是文件路径的16进制值，返回的结果中就会将该文件的内容返回到对应列中。

#防御sql注入
1.在java中使用prepareStatement接口，该接口在生成sql时会将输入的字符串逐个字符检查并转义来防止注入。

2.对输入的类型进行判断，在id=1的情况下，只允许输入参数为整形，而不允许为字符串。

3.自己实现函数对输入的sql关键词过滤。

#相关资料
下面这个网站中有大量的sql注入资料，包括入门三部曲，攻防教程等等，值得一看。

[http://netsecurity.51cto.com/art/201108/287651.htm](http://netsecurity.51cto.com/art/201108/287651.htm)