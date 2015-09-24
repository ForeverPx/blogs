#Hexo免输入密码部署到github

在使用(hexo d)命令部署hexo到github时，每次都要输入用户名和密码，这会使得你预览你博客的修改显得更加繁琐。下面的方法将使你摆脱这种痛苦。
首先，在系统环境变量中设置一个环境变量

```bash
HOME
%USERPROFILE%
```

如下图：

![r](http://i3.tietuku.com/d72e170806f25bff.jpg)

接着在你的用户目录"C:\\Users\\username"下新建一个叫"*_netrc*"的文件
编辑这个文件

```bash
machine github.com
login username
password password
```

设置好这些后，当你再次部署时，就不用输入用户名和密码了。

文章作者：foreverpx