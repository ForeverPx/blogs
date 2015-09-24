#解决Mac java.net Local host name unknown error的方法

##现象
在Mac上启动tomcat时，报了如下错误：

*Error: Exception thrown by the agent : java.net.MalformedURLException: Local host name unknown: java.net.UnknownHostException: XXXX: XXXX: nodename nor servname provided, or not known*

##解决方法
查看 */ect/hosts* 文件的内容:
*127.0.0.1 localhost*
好像没什么异常，但是通过
```bash
scutil ––get HostName 
```
命令查看返回的确实空，所以只有手动设置默认的host了
```bash
scutil ––set HostName "localhost"
```
此处的localhost必须存在于hosts文件中。

文章原文链接：[https://github.com/ForeverPx/blogs/blob/master/2015/07/mac_tomcat_localhost_unknown.md](https://github.com/ForeverPx/blogs/blob/master/2015/07/mac_tomcat_localhost_unknown.md)
转载请注明出处。


