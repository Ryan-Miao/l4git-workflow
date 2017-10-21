---
title: Git 工作流
date: 2017-10-18 21:44:53
tags:
- Git

Categories:
- Git

---

![](https://camo.githubusercontent.com/c74535a35ba195450eabbfa44a95bc1cc8e89182/68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f636f6c6c61626f726174696e672f636f6d706172696e672d776f726b666c6f77732f676974666c6f772d776f726b666c6f772f30342e737667)

# 前言

一直在使用git做版本控制，也一直工作很顺利，直到和别人发生冲突的时候。这才注意到git 工作流并不是那么简单。比如，之前遇到的[清理历史](http://www.cnblogs.com/woshimrf/p/git-rebase.html)。百度到的资料很多，重复性也很多，但实践性操作很少，我很难直接理解其所表达的含义。直接望文生义经常得到错误的结论，只能用时间去检验真理了，不然看到的结果都是似懂非懂，最后还是一团糟。

# 学习git工作流

# 1. 最简单的使用，不推荐

## 1.1.创建仓库
```
$ pwd
/home/ryan/workspace/l4git-workflow
$ touch readme.md
$ ls
readme.md
$ touch .gitignore
$ git init
初始化空的 Git 仓库于 /home/ryan/workspace/l4git-workflow/.git/
$ touch test.txt
$ git add .
$ git commit -m "init"
[master （根提交） dae77d6] init
 3 files changed, 12 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 readme.md
 create mode 100644 test.txt
$ git remote add origin git@github.com:Ryan-Miao/l4git-workflow.git
$ git push -u origin master
对象计数中: 5, 完成.
Delta compression using up to 4 threads.
压缩对象中: 100% (3/3), 完成.
写入对象中: 100% (5/5), 388 bytes | 0 bytes/s, 完成.
Total 5 (delta 0), reused 0 (delta 0)
To git@github.com:Ryan-Miao/l4git-workflow.git
 * [new branch]      master -> master
分支 master 设置为跟踪来自 origin 的远程分支 master。
```

![](https://camo.githubusercontent.com/80e8ec5e80b76f403fb19f5d601302655d2f8f6c/68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f636f6c6c61626f726174696e672f636f6d706172696e672d776f726b666c6f77732f63656e7472616c697a65642d776f726b666c6f772f30312e737667)

## 1.2. 模拟用户A
``` 
git clone git@github.com:Ryan-Miao/l4git-workflow.git
git checkout a
touch a.txt
//write one
//....
$ git add .
$ git commit -m "one"
[a 53ff45e] one
 2 files changed, 34 insertions(+), 2 deletions(-)
 create mode 100644 a.txt
```
此时，a还没有提交到`origin`。 git log 如下：

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/a-one.png)


## 1.3. 模拟用户B
```
git clone git@github.com:Ryan-Miao/l4git-workflow.git
git checkout b
```
```
$ touch b.txt
```
//write something 
//...
```
$ git add .
$ git commit -m "b write one"
[b 847078e] b write one
 1 file changed, 1 insertion(+)
 create mode 100644 b.txt
```

//write something
//....
 ```
$ git add .
$ git commit -m "b write two"
[b 3f30f41] b write two
 1 file changed, 2 insertions(+), 1 deletion(-)
```

此时，git log如下
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/b-two.png)


## 1.4. 模拟用户A
A和B分别是在本地开发，所以这种顺序是未知的，也许A比B先commit一次，也许B先commit一次。这里的先后是指commit的时间戳。但都是在本地提交的代码。
write something
```
git add .
git commit -m "a write two"
```
wirte something
```
git add .
git commit -m "write three"
```
A push to server branch `a`

```
$ git push origin a:a
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:Ryan-Miao/l4git-workflow.git
 * [new branch]      a -> a
```
A created a Pull Request

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/a-push.png)
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/a-pr.png)


## 1.5. 模拟用户C
C review the PR and then merged it.

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-merge.png)
此时，github的历史如下：
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/github-a.png)
可以看出，merge的时候多了一次commit，message默认为 `Merge pull request #1 from Ryan-Miao/a...`
现在看起来，只有a一个人的历史记录，还算清楚，a做了3次提交。

## 1.6. 模拟用户B
用户B提交前先pull master，更新最新的代码到本地，防止冲突。
```
 git fetch
 git merge origin/master
```
此时log看起来有点乱。如下：
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/b-merge-master.png)
让人感到混乱的是b原来的历史只有自己的提交，更新了master到本地之后，历史记录被插入了master中的历史。于是，发现原来自己干净的历史被中间插入多次commit。甚至两次merge master的日志显得又长又碍眼。但不管怎么说，B还是要提交的。

于是，B提交到远程分支b：
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/b-push.png)

## 1.7. 模拟用户C
这时候，A完成了feature a，然后提了PR，然后找他人C merge了。而后，B也完成了feature b，提了PR，需要review and merge。 C review之后，approved， 然后D review， D merge。

此时，项目基本走上正规。feature一个一个添加进去，重复之前的工作流程： fetch -》 work -》 commit -》 push -》 PR -》 merged。
然后，项目历史就变成了这样：

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/github-log2.png)

一眼大概看起来还好，每次都能看到提交历史，只要不是message写的特别少，差不多可以理解最近提交的内容。然而，仔细一看，顺序好像不对。目前一共两个feature，但历史却远远超过2个。没关系，保证细粒度更容易体现开发进度。然而，这些历史并不是按照feature的发布顺序，那么，当我想要找到feature a的时候就很难串联起来。如果commit足够多，时间跨度足够大，甚至根本看不出来feature a到底做了哪些修改。

这时候想要使用图形化git 历史工具来帮助理解历史：
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/tu.png)

这里，还好，还勉强能看出走向。但当10个上百个人同时开发的话，线简直不能看了，时间跨度足够大的话，线也看不完。


因此，这种模式，正是我们自己当前采用的模式。差评。这还不算完，后面更大的困难来了。最先发布的feature a出了问题，必须回滚。怎么做到。关于[回滚](http://www.cnblogs.com/woshimrf/p/5702696.html)，就是另一个话题了。 但我们应该知道使用`revert`而不是`reset`. 但revert只能回滚指定的commit，或者连续的commit，而且revert不能revert merge操作。这样，想回滚feature a, 我们就要找到a的几次提交的版本号，然后由于不是连续的，分别revert。这会造成复杂到不想处理了。好在github给了方便的东西，PR提供了revert的机会。找到以前的PR。

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/revert.png)

但是，这绝对不是个好操作！



----

# 2. 推荐的工作流程

造成上述现象的原因是因为各自异步编程决定的。因为每个人都可以随时间提交，最后合并起来的时候以提交时间戳来作为序列的依据，就会变成这样。因此，当需要提交的远程服务器的时候，如果能重写下commit的时间为当前时间，然后push到服务端，历史就会序列到最后了。

## 2.1 模拟用户C
C用户新下载代码。
```
$ git clone git@github.com:Ryan-Miao/l4git-workflow.git c正克隆到 'c'...
remote: Counting objects: 28, done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 28 (delta 8), reused 22 (delta 4), pack-reused 0
接收对象中: 100% (28/28), 5.90 KiB | 0 bytes/s, 完成.
处理 delta 中: 100% (8/8), 完成.
检查连接... 完成。

```
然后编辑，提交
```
$ cd c
$ git config user.name "C"
$ ls
a.txt  b.txt  readme.md  test.txt
$ vim c.txt
$ git add .
$ git commit -m "C write one"
[master cf3f757] C write one
 1 file changed, 2 insertions(+)
 create mode 100644 c.txt

```



## 2.2 模拟用户D

同时，D也需要开发新feature
```
$ git clone git@github.com:Ryan-Miao/l4git-workflow.git d正克隆到 'd'...
remote: Counting objects: 28, done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 28 (delta 8), reused 22 (delta 4), pack-reused 0
接收对象中: 100% (28/28), 5.90 KiB | 0 bytes/s, 完成.
处理 delta 中: 100% (8/8), 完成.
检查连接... 完成。
$ cd d
/d$ git config user.name "D"
/d$ vim d.txt
/d$ git add .
/d$ git commit -m "d write one"
[master db7a6e9] d write one
 1 file changed, 1 insertion(+)
 create mode 100644 d.txt

```

## 2.3 C继续开发

```
$ vim c.txt
$ git add .
$ git commit -m "c write two"
[master 01b1210] c write two
 1 file changed, 1 insertion(+)

```

## 2.4 D继续开发
```
/d$ vim d.txt
/d$ git add .
/d$ git commit -m "d write two"
[master a1371e4] d write two
 1 file changed, 1 insertion(+)

```

## 2.5 C 提交
```
$ vim c.txt 
$ git add .
$ git commit -m "c write three"
[master 13b7dde] c write three
 1 file changed, 1 insertion(+)

```
C开发结束，提交到远程
```
$ git status
位于分支 master
您的分支领先 'origin/master' 共 3 个提交。
  （使用 "git push" 来发布您的本地提交）
无文件要提交，干净的工作区
$ git push origin master:C
对象计数中: 9, 完成.
Delta compression using up to 4 threads.
压缩对象中: 100% (6/6), 完成.
写入对象中: 100% (9/9), 750 bytes | 0 bytes/s, 完成.
Total 9 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 1 local object.
To git@github.com:Ryan-Miao/l4git-workflow.git
 * [new branch]      master -> C

```

## 2.6 C 提PR

然后，create a Pull Request.
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-pr.png)


## 2.7 C修改再push
然后，发现还有个bug要修复，再次修改提交到远程C
```
$ vim c.txt 
$ git add .
$ git commit -m "C finish something else"
[master 2c5ff94] C finish something else
 1 file changed, 1 insertion(+), 1 deletion(-)
$ git push origin master:C
对象计数中: 3, 完成.
Delta compression using up to 4 threads.
压缩对象中: 100% (3/3), 完成.
写入对象中: 100% (3/3), 301 bytes | 0 bytes/s, 完成.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To git@github.com:Ryan-Miao/l4git-workflow.git
   13b7dde..2c5ff94  master -> C

```

## 2.8 C发现提交次数过多，历史太乱，合并部分历史
这时，发现一个问题，由于C在开发过程中提交了多次，而这几次提交的message其实没有多大意思，只是因为C可能为了保存代码，也可能是暂存。总之，C的前3次提交的message的含义其实是一样的，都是创建C文件，都是一个主题，那么为了维护历史的干净。最好把这3条信息合并成一条`C create file c.txt`。

参考[git 合并历史](http://www.cnblogs.com/woshimrf/p/git-rebase.html)，我们需要将3次历史合并成显示为一次。

查看git历史，找到需要合并的起始区间
```
$ git log --oneline
2c5ff94 C finish something else
13b7dde c write three
01b1210 c write two
cf3f757 C write one
7151f4c 记录操作。
0bfe562 Merge pull request #2 from Ryan-Miao/b_remote
d81ce20 Merge remote-tracking branch 'origin/master' into b
2d74cfb Merge pull request #1 from Ryan-Miao/a
b90a3dd write three
4b1629e a write two
3f30f41 b write two
847078e b write one
53ff45e one
dae77d6 init

```
显然，是要合并`cf3f757`到`13b7dde`。那么找到前一个的版本号为`7151f4c`
```
git rebase - i 7151f4c
```
然后进入交互界面，因为我们想要把第3次和第2次以及第1次提交信息合并。将第3次的类型修改为`squash`, 意思是和第2次合并。然后将第2次的类型修改为`squash`, 同样是指合并的前一个commit。
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-rebase-i.png)
不同git的交互略有不同，之前在windows上的git bash是完全按照vim的命令修改的。本次测试基于Ubuntu，发现存档命令为`ctel + X`。确认后进入下一个界面，合并3次提交后需要一个message

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-rebase-i-2.png)

删除或者anyway you like， 更改message。存档。完成。

```
$ git rebase -i 7151f4c
[分离头指针 e3764c5] c create  file c.txt
 Date: Fri Oct 20 22:06:24 2017 +0800
 1 file changed, 4 insertions(+)
 create mode 100644 c.txt
Successfully rebased and updated refs/heads/master.
```

**Tips**   
当在rebase过程中出现了失误，可以使用`git rebase --abort`返回初始状态。如果发现冲突，则可以解决冲突，然后`git  rebase --continue` . 
>好像已有 rebase-merge 目录，我怀疑您正处于另外一个变基操作
>过程中。 如果是这样，请执行
>	git rebase (--continue | --abort | --skip)
>如果不是这样，请执行
>	rm -fr "/home/ryan/temp/c/.git/rebase-merge"
>然后再重新执行变基操作。 为避免丢失重要数据，我已经停止当前操作。


此时，查看log， 显然，C的那三次提交已经合并了。
```
$ git log --oneline
50b9fe9 C finish something else
e3764c5 c create  file c.txt
7151f4c 记录操作。
0bfe562 Merge pull request #2 from Ryan-Miao/b_remote
d81ce20 Merge remote-tracking branch 'origin/master' into b
2d74cfb Merge pull request #1 from Ryan-Miao/a
b90a3dd write three
4b1629e a write two
3f30f41 b write two
847078e b write one
53ff45e one
dae77d6 init
```

## 2.9 C再次push

之前的push已经不能用了。需要开新分之推送过去。因为××rebase 只能在本地分支做。不去修改公共分支××。
```
$ git push origin master:C
To git@github.com:Ryan-Miao/l4git-workflow.git
 ! [rejected]        master -> C (non-fast-forward)
error: 无法推送一些引用到 'git@github.com:Ryan-Miao/l4git-workflow.git'
提示：更新被拒绝，因为推送的一个分支的最新提交落后于其对应的远程分支。
提示：检出该分支并整合远程变更（如 'git pull ...'），然后再推送。详见
提示：'git push --help' 中的 'Note about fast-forwards' 小节。
```
选择推送的新分支C2
```
$ git push origin master:C2
对象计数中: 6, 完成.
Delta compression using up to 4 threads.
压缩对象中: 100% (5/5), 完成.
写入对象中: 100% (6/6), 569 bytes | 0 bytes/s, 完成.
Total 6 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
To git@github.com:Ryan-Miao/l4git-workflow.git
 * [new branch]      master -> C2

```
创建新的PR
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-new.png)


## 2.10 新的merge方式： rebase
通过开始的普通流程发现，每次merge的时候，都会多出一条新的提交信息，这让历史看起来很奇怪。那么，可以选择rebase到master，变基，就是重新以master为基本，把当前的提交直接移动到master的后面。不会因为提交时间的离散导致多次commit的message被拆散。 选择 `rebase and merge`
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-merge-rebase.png)

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-rebase-merge.png)

这时候，可以看到C提交的两次信息都是最新的，没有发生交叉。而且也没有产生多余的merge信息。
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-log-clean.png)

有人会问，那么岂不是看不到PR的地址了。点开C的历史。可以看到message下方是有PR的编号的：
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-detail.png)

对了，刚开始的PR要记得close
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-pr-close.png)

### 2.11 这时候D也完成了
```
/d$ git push origin master:D
对象计数中: 10, 完成.
Delta compression using up to 4 threads.
压缩对象中: 100% (7/7), 完成.
写入对象中: 100% (10/10), 4.49 KiB | 0 bytes/s, 完成.
Total 10 (delta 2), reused 4 (delta 1)
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
To git@github.com:Ryan-Miao/l4git-workflow.git
 * [new branch]      master -> D

```
提PR， 这时候，如果采用merge：
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/d-merge.png)

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/d-log.png)

结果必然发现，1） d提交message被按照时间分散插入历史了(被插入到c的历史之前)， 2）多了一次 `Merge pull request #5 from Ryan-Miao/D..`的提交信息。同开头所述一样，历史开始变得混乱了。那么，这种问题怎么办呢？



### 2.12 提交前rebase

就像C rebase后merge到master一样。我们一样可以在本地做到这样的事情。在本地rebase，让我们本次feature的提交全部插到master节点之后，有序，而且容易revert。
本次，以新的E和F交叉commit为例子，最终将得到各自分开的历史

E:
```
$ git clone git@github.com:Ryan-Miao/l4git-workflow.git e
正克隆到 'e'...
remote: Counting objects: 52, done.
remote: Compressing objects: 100% (33/33), done.
remote: Total 52 (delta 18), reused 36 (delta 7), pack-reused 0
接收对象中: 100% (52/52), 7.91 KiB | 0 bytes/s, 完成.
处理 delta 中: 100% (18/18), 完成.
检查连接... 完成。

$ cd e
/e$ vim e.txt
/e$ git add .
/e$ git config user.name "E"
/e$ git commit -m "e commit one"
[master 77ecd73] e commit one
 1 file changed, 1 insertion(+)
 create mode 100644 e.txt

```

F:
```
$ git clone git@github.com:Ryan-Miao/l4git-workflow.git f
正克隆到 'f'...
remote: Counting objects: 52, done.
remote: Compressing objects: 100% (33/33), done.
remote: Total 52 (delta 18), reused 36 (delta 7), pack-reused 0
接收对象中: 100% (52/52), 7.91 KiB | 0 bytes/s, 完成.
处理 delta 中: 100% (18/18), 完成.
检查连接... 完成。

$ cd f
$ vim f.txt
$ git config user.name "F"
$ git add .
$ git commit -m "d write one"
[master b41f8c5] d write one
 1 file changed, 2 insertions(+)
 create mode 100644 f.txt

```

E:
```
/e$ vim e.txt
/e$ git add .
/e$ git commit -m "e write two"
[master 2b8c9fb] e write two
 1 file changed, 1 insertion(+)

```

F:
```
$ vim f.txt
$ git add .
$ git commit -m "f write two"
[master de9051b] f write two
 1 file changed, 1 insertion(+)

```

E:
```
/e$ vim e.txt 
/e$ git add .
/e$ git commit -m "e write three"
[master b1b9f6e] e write three
 1 file changed, 2 insertions(+)

```
这时候，e完成了，需要提交。提交前先rebase：

```
/e$ git fetch
/e$ git rebase origin/master
当前分支 master 是最新的。
```
然后，再提交

```
/e$ git push origin master:E
对象计数中: 9, 完成.
Delta compression using up to 4 threads.
压缩对象中: 100% (6/6), 完成.
写入对象中: 100% (9/9), 753 bytes | 0 bytes/s, 完成.
Total 9 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 1 local object.
To git@github.com:Ryan-Miao/l4git-workflow.git
 * [new branch]      master -> E

```
然后， PR, merge.

同样F:
```
$ git status
位于分支 master
您的分支领先 'origin/master' 共 2 个提交。
  （使用 "git push" 来发布您的本地提交）
无文件要提交，干净的工作区
$ git fetch 
remote: Counting objects: 12, done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 12 (delta 6), reused 6 (delta 3), pack-reused 0
展开对象中: 100% (12/12), 完成.
来自 github.com:Ryan-Miao/l4git-workflow
   24c6818..f36907c  master     -> origin/master
 * [新分支]          E          -> origin/E
$ git rebase origin/master
首先，回退分支以便在上面重放您的工作...
应用：d write one
应用：f write two

$ git push origin master:F
对象计数中: 6, 完成.
Delta compression using up to 4 threads.
压缩对象中: 100% (4/4), 完成.
写入对象中: 100% (6/6), 515 bytes | 0 bytes/s, 完成.
Total 6 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
To git@github.com:Ryan-Miao/l4git-workflow.git
 * [new branch]      master -> F

```

PR， rebase and merge。 这时候看history：

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/f-rebase.png)

按照前几次的做法，E和F交叉在本地提交，每次commit的时间戳也是交叉，最终合并到master的时候，历史并没有被拆散。而是像我们期待的一样，顺序下来。这才是我们想要的。通过看图形化界面也能看出区别：

绿色的线是master
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/final.png)


那么，操作便是fetch-》rebase。事实上，可以二合一为：

```
git pull --rebase origin master
```
![](https://camo.githubusercontent.com/f1ce447510fcd8c37f0dcb0f677de4896907e607/68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f636f6c6c61626f726174696e672f636f6d706172696e672d776f726b666c6f77732f63656e7472616c697a65642d776f726b666c6f772f31312e737667)










# 金科玉律
1. 想维持树的整洁，方法就是：在git push之前，先git fetch，再git rebase。

```
git fetch origin master
git rebase origin/master
git push
```

或者
```
git pull --rebase origin master
```

2. 绝对不可以在公共分支上reset，也不要用--force
3. 单独功能的多次提交要学会合并提交，保持提交的简洁。
4. 提交message尽量能概括修改内容。



# 参考来源
- https://github.com/geeeeeeeeek/git-recipes/wiki/3.5-%E5%B8%B8%E8%A7%81%E5%B7%A5%E4%BD%9C%E6%B5%81%E6%AF%94%E8%BE%83
- https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E5%86%99%E5%8E%86%E5%8F%B2
- https://segmentfault.com/q/1010000000430041
