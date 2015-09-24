​IntellijIDEA使用GIT Push的问题

前不久从Eclipse转到了Intellij，发现Intellij真是一个非常强大的IDE，基于jetbrains，另一个类似的是webstorm，一个前端开发的利器。

今天在IDEA中使用git时，git commit没问题，但是git push处于一直执行的状态，IDEA下方的进度条也是一直在跑，过了很久也没提交成功，无奈之下只有结束掉git进程。后来打开IDEA左下角中的version control console栏，发现里面报了如下错误

```bash
cannot create \System32\config\systemprofile\.ssh
```

IDEA在push的时候要在systemprofile文件夹中创建.ssh文件，但是失败了。

根据以往的经验，这种不能创建文件或者文件夹的问题基本可以断定不是文件被占用，就是没有操作权限。果然，用管理员身份运行IDEA就可以push成功了。

文章作者：foreverpx

