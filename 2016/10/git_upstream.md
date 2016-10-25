# git关联本地与远程分支

当我们在本地新建一个叫**devtest**的分支的时候，我们希望他与git远程上的某个分支进行关联。

假设远程上的那个分支也叫**devtest**，如果我们直接去pull代码，会报下面的错

```bash
上午10:54:33 Branch devtest was created (show balloon)
上午10:54:36 Can't update: no tracked branch
           No tracked branch configured for branch devtest.
           To make your branch track a remote branch call, for example,
           git branch --set-upstream devtest origin/devtest (show balloon)
```

提示我们需要用 --set-upstream 去关联这两个分支，命令是

```bash
git branch --set-upstream devtest origin/devtest
```

当你执行这句命令之后，他又会提示你**--set-upstream**要换成**--set-upstream-to**命令

--set-upstream-to 在新版本git中已经替代了 --set-upstream， 并且后面跟随的两个参数要对调一下，如下面命令

```bash
git branch --set-upstream-to origin/devtest devtest 
```

远程分支在前，本地分支在后。

关联之后就可以正常的pull代码了。

