[TOC]
## 第一次安装设置
```
#因为git是分布式,所以每个机器都要自报家门
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com" 
```
## 创建版本仓库
```
$ git init #创建版本仓库  
$ git add #添加文件到版本仓库(可同时添加多个文件空格分割)
$ git commit -m '注释内容' #提交版本  
```
## 版本比对
```
$ git status #查看版本库状态以及文件修改状态
$ git diff 文件名 #查看文件异同
```
## 版本回退
```
$ git log [--pretty] #显示从最近到最远的提交日志.--pretty 只显示一行
```
> 和SVN不一样,GIT每个提交版本的commit id不是1,2,3...的数字而是一个SHA1计算出来的十六进制数字.因为GIT是分布式控制系统,如果用1,2,3...数字表示多人协作会重复.

```
HEAD #表示最新提交的版本
HEAD^ #表示上一个提交版本
HEAD~100 #表示往上100个版本(~ 代替多个 ^)
|
#git reset 版本号
$ git reset --hard HEAD^ #回退到上个版本
```

> git reset 后面如果是版本号不用写全,只写前几位就可以了.GIT版本回退非常快,因为GIT在内部有个指向当前版本的HEAD指针,当回退版本时GIT仅仅是把HEAD从指向当前版本改为指向回退版本(如下图),顺便把工作区更新了.

![当前版本](img/QQ20171117-112922@2x.png)

![版本回退](img/QQ20171117-112940@2x.png)

> 由于git log仅显示从当前版本到历史版本的纪录,如果回退了某个版本,又想还原到最新版本怎么办呢?

```
$ git reflog #此命令用来记录你的第一次命令
```
## 工作区和暂存区
> git和其它版本控制系统,如SVN的一个不同之处就是有暂存区的概念.
`工作区`:就是电脑能看到的仓库文件夹目录.
`版本库`:工作区内的隐藏目录.git,这个不算工作区,而是git的版本库.
GIT版本库里存了很多东西,其中最重要的就是stage(或者叫index)的暂存区,还有GIT为我们自动创建的第一个分支master,以及一个指向master的指针HEAD.
实际上git add就是把所有修改放到暂存区,git commit是把暂缓区的修改提交到分支.
**因此,修改一个文件后必须要添加(git add)到暂存区,才能提交(git commit)到分支,否则提交无效**

![版本库](img/QQ20171117-115013@2x.png)
## 撤销修改
```
$ git checkout -- 文件名 #撤销工作区文件的修改(如果没有--应变成了切换分支的命令)
$ git reset HEAD 文件名 #撤销暂存区文件修改(HEADH表示撤销到最新版本)
```
## 删除文件
```
$ git rm 文件名 #从版本库中删除文件
```
> GIT删除(git rm)文件后,直接提交(git commit)即可,不需要再添加到暂存区(git add).
> 如果不小心删错了,可以直接撤销修改(git checkout -- 文件名)
> 如果文件以经提交到版本库,那么永远不用担心误删,但只能恢复到最新版本库,最新修改将丢失.

## 远程仓库
```
$ git remote add origin 远程仓库地址 #关联远程仓库(origin是git远程仓库的默认名,可以修改)
$ git push #本地内容推送到远程仓库
```
> 关联后第一次推送仓库的所有内容使用命令:git push -u origin master
> 此后,每次推送本地修改内容可以使用命令:git push origin master

```
	$ git clone 远程仓库地址 #克隆远程仓库到本地
```
