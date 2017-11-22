[TOC]
## 考察与比较
- show命令  

```
$ git show [-times] 显示最近几次提交对象(默认一次)
```
- diff命令

```
$ git diff #查看工作区与暂存区的差异
$ git diff --cached [commitId] #查看暂存区与指定提交(默认HEAD)的差异
$ git diff [commitId) #查看工作目录与指定提交的差异
$ git diff commitId commitId #查看两次提交的差异
$ git diff 分支名 #工作区与指定分支的差异
$ git diff 分支名 分支名 #查看两指定分支的差异 
``` 
- shortlog命令
	显示最近提交次数与每次提交消息

- describe命令

```
#查看指定提交或标签信息
$ git describe [commitId|标签名]
$ git describe --all [commitId|标签名]
```
- reflog命令
显示全部操作日志(log仅显示从当前版本的日志,如果回退之后,再想回退到之前版本,可以使用该命令查看commitId)

