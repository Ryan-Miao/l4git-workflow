# 学习git工作流

# 1.创建仓库
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

# 2. 模拟用户A
``` 
git clone git@github.com:Ryan-Miao/l4git-workflow.git
git checkout a
touch a.txt
//write one

```
