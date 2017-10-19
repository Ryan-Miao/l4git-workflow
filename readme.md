
![](https://camo.githubusercontent.com/c74535a35ba195450eabbfa44a95bc1cc8e89182/68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f636f6c6c61626f726174696e672f636f6d706172696e672d776f726b666c6f77732f676974666c6f772d776f726b666c6f772f30342e737667)

# 前言

一直在使用git做版本控制，也一直工作很顺利，直到和别人发生冲突的时候。这才注意到git 工作流并不是那么简单。比如，之前遇到的[清理历史](http://www.cnblogs.com/woshimrf/p/git-rebase.html)。百度到的资料很多，重复性也很多，但实践性操作很少，我很难直接理解其所表达的含义。直接望文生义经常得到错误的结论，只能用时间去检验真理了，不然看到的结果都是似懂非懂，最后还是一团糟。

# 学习git工作流

## 1. 最简单的使用，不推荐

## 1.创建仓库
```
ryan@ryan-900X5L:~/workspace/l4git-workflow$ pwd
/home/ryan/workspace/l4git-workflow
ryan@ryan-900X5L:~/workspace/l4git-workflow$ touch readme.md
ryan@ryan-900X5L:~/workspace/l4git-workflow$ ls
readme.md
ryan@ryan-900X5L:~/workspace/l4git-workflow$ touch .gitignore
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git init
初始化空的 Git 仓库于 /home/ryan/workspace/l4git-workflow/.git/
ryan@ryan-900X5L:~/workspace/l4git-workflow$ touch test.txt
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git add .
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git commit -m "init"
[master （根提交） dae77d6] init
 3 files changed, 12 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 readme.md
 create mode 100644 test.txt
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git remote add origin git@github.com:Ryan-Miao/l4git-workflow.git
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git push -u origin master
对象计数中: 5, 完成.
Delta compression using up to 4 threads.
压缩对象中: 100% (3/3), 完成.
写入对象中: 100% (5/5), 388 bytes | 0 bytes/s, 完成.
Total 5 (delta 0), reused 0 (delta 0)
To git@github.com:Ryan-Miao/l4git-workflow.git
 * [new branch]      master -> master
分支 master 设置为跟踪来自 origin 的远程分支 master。
```

## 2. 模拟用户A
``` 
git clone git@github.com:Ryan-Miao/l4git-workflow.git
git checkout a
touch a.txt
//write one
//....
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git add .
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git commit -m "one"
[a 53ff45e] one
 2 files changed, 34 insertions(+), 2 deletions(-)
 create mode 100644 a.txt
```
此时，a还没有提交到`origin`。 git log 如下：

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/a-one.png)


## 3. 模拟用户B
```
git clone git@github.com:Ryan-Miao/l4git-workflow.git
git checkout b
```
```
ryan@ryan-900X5L:~/workspace/l4git-workflow$ touch b.txt
```
//write something 
//...
```
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git add .
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git commit -m "b write one"
[b 847078e] b write one
 1 file changed, 1 insertion(+)
 create mode 100644 b.txt
```

//write something
//....
 ```
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git add .
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git commit -m "b write two"
[b 3f30f41] b write two
 1 file changed, 2 insertions(+), 1 deletion(-)
```

此时，git log如下
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/b-two.png)


## 4. 模拟用户A
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
ryan@ryan-900X5L:~/workspace/l4git-workflow$ git push origin a:a
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:Ryan-Miao/l4git-workflow.git
 * [new branch]      a -> a
```
A created a Pull Request

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/a-push.png)
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/a-pr.png)


## 5. 模拟用户C
C review the PR and then merged it.

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/c-merge.png)
此时，github的历史如下：
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/github-a.png)
可以看出，merge的时候多了一次commit，message默认为 `Merge pull request #1 from Ryan-Miao/a...`
现在看起来，只有a一个人的历史记录，还算清楚，a做了3次提交。

## 6. 模拟用户B
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

## 7. 模拟用户C
这时候，A完成了feature a，然后提了PR，然后找他人C merge了。而后，B也完成了feature b，提了PR，需要review and merge。 C review之后，approved， 然后D review， D merge。

此时，项目基本走上正规。feature一个一个添加进去，重复之前的工作流程： fetch -》 work -》 commit -》 push -》 PR -》 merged。
然后，项目历史就变成了这样：

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/github-log2.png)

一眼大概看起来还好，每次都能看到提交历史，只要不是message写的特别少，差不多可以理解最近提交的内容。然而，仔细一看，顺序好像不对。目前一共两个feature，但历史却远远超过2个。没关系，保证细粒度更容易体现开发进度。然而，这些历史并不是按照feature的发布顺序，那么，当我想要找到feature a的时候就很难串联起来。如果commit足够多，时间跨度足够大，甚至根本看不出来feature a到底做了哪些修改。

这时候想要使用图形化git 历史工具来帮助理解历史：
![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/tu.png)

这里，还好，还勉强能看出走向。但当10个上百个人同时开发的话，线简直不能看了，时间跨度足够大的话，线也看不完。


因此，这种模式，正是我们自己当前采用的模式。差评。这还不算完，后面更大的困难来了。最先发布的feature a出了问题，必须回滚。怎么做到。关于[回滚](http://www.cnblogs.com/woshimrf/p/5702696.html)，就是另一个话题了。 但我们应该知道使用`revert`而不是`reset`. 但revert只能回滚指定的commit，或者连续的commit，而且revert不能revert merge操作。这样，想回滚feature a, 我们就要找到a的几次提交的版本号，然后由于不是连续的，分别revert。这会造成复杂到不想处理了。好在github给了方便的东西，PR提供了revert的体会。找到以前的PR。

![](http://oe20lp6p0.bkt.clouddn.com/blog/2017/git/revert.png)

但是，这绝对不是个好操作！

《未完待续》







# 金科玉律
1. 想维持树的整洁，方法就是：在git push之前，先git fetch，再git rebase。

```
git fetch origin master
git rebase origin/master
git push
```

2. 绝对不可以在公共分支上reset，也不要用--force
3. 单独功能的多次提交要学会合并提交，保持提交的简洁。
4. 提交message尽量能概括修改内容。



# 参考来源
- https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E5%86%99%E5%8E%86%E5%8F%B2
- https://github.com/geeeeeeeeek/git-recipes/wiki/3.5-%E5%B8%B8%E8%A7%81%E5%B7%A5%E4%BD%9C%E6%B5%81%E6%AF%94%E8%BE%83
- https://segmentfault.com/q/1010000000430041
