# 什么是git



* git是Linux发明者Linus开发的一款新时代的版本控制系统，它是一种记录一个或若干个文件内容的变化，以便将来查阅特定版本的修改情况

![image-20240106153146149](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240106153146149.png)

# git指令

* 首先创建一个文件夹，作为git仓库目录，并且切换到该目录下

```bash
git status#查看状态
E:\桌面\Resource\temp>git status
fatal: not a git repository (or any of the parent directories): .git
#意思是当前目录不是一个git仓库

git init#此时该目录就成为了一个git仓库
E:\桌面\Resource\temp>git init
Initialized empty Git repository in E:/桌面/Resource/temp/.git/
-----------------------------------------------------------------------------------------------------------
git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        test.txt

nothing added to commit but untracked files present (use "git add" to track)
#在master分支下，test.txt目录没有被跟踪，没有被提交在git仓库中，提示使用git add 可以跟踪这个文件
-----------------------------------------------------------------------------------------------------------
git add test.txt
#可以将test.txt文件提交到git仓库中
-----------------------------------------------------------------------------------------------------------
E:\桌面\Resource\temp>git add test.txt

E:\桌面\Resource\temp>git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   test.txt
        
#changes to be committed,意思test.txt文件等待被提交，可以使用git rm --cached指令从缓存中移除
-----------------------------------------------------------------------------------------------------------
git commit -m 'first commit'
#commit 代表提交的意思，-m指提交的信息
E:\桌面\Resource\temp>git commit -m 'firstcommit'
[master (root-commit) 7cea0d5] 'firstcommit'
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test.txt

E:\桌面\Resource\temp>git status
On branch master
nothing to commit, working tree clean
-----------------------------------------------------------------------------------------------------------
git log
#可以查看所有产生commit的记录
E:\桌面\Resource\temp>git log
commit 7cea0d53bce826370408d3a894250cf88cbed561 (HEAD -> master)
Author: xun <1015134504@qq.com>
Date:   Sat Jan 6 15:57:48 2024 +0800

    'firstcommit'
-----------------------------------------------------------------------------------------------------------
git add && git commit
#git add 是把文件先添加到一个暂存区，可以理解成是一个缓存区域，临时保存所有的修改，而git commit才是真正的提交，这样的好处就是可以防止你错误的提交。
-----------------------------------------------------------------------------------------------------------
git branch
#branch 即分支的意思，不同的的分支，在修改代码时互不影响，等项目要收工时，将分支一合并，
git init初始化仓库时会生成一个住分支master，也是默认分支

E:\桌面\Resource\temp>git branch
* master

怎么新建一个分支呢?
git branch a
#创建一个名为a的分支
E:\桌面\Resource\temp>git branch a

E:\桌面\Resource\temp>git branch
  a
* master
#master分支前面有一个*，这代表当前所在的分支仍然为master分支，那么怎么切换到a分支呢？
git checkout a
#切换分支
E:\桌面\Resource\temp>git checkout a
Switched to branch 'a'

E:\桌面\Resource\temp>git branch
* a
  master
  
#怎么将新建分支与切换分支合并起来呢？
git checkout -b a
#新建一个分支a，并且切换到分支a
-----------------------------------------------------------------------------------------------------------
git merge
#将代码合并到master分支上，然后发布，首先切换到master分支，之后使用命令git merge a，意思就是将分支a合并到master分支。
-----------------------------------------------------------------------------------------------------------
git branch -d
#删除分支a，git branch -d a
#强制删除git branch -D
E:\桌面\Resource\temp>git branch
  a
* master

E:\桌面\Resource\temp>git branch -d a
Deleted branch a (was 7cea0d5).

E:\桌面\Resource\temp>git branch
* master
-----------------------------------------------------------------------------------------------------------

git push origin master
#本地代码有了更新，需要将本地代码推送到远程仓库，这样本地仓库就与远程仓库同步了
git pull origin master
#将远程仓库的代码拉下来，保证两端的代码同步，
git clone 仓库地址
#克隆某个项目








```

