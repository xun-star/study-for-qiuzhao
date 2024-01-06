## 用户名和邮箱

>
>
>每一次的提交都会产生一条log，这条log记录了提交人的姓名和邮箱，方便查看，所以我们需要设置自己的邮箱和用户名

```bash
git config --global user.name "用户名"
git config --global user.email "邮箱"
#以上进行了全局配置
```

## alias

>
>
>alias意思是别名，这样可以使得我们的操作变得更加的简单快捷

```bash
git config --global alias.br branch
E:\桌面\Resource\temp>git config --global alias.br branch

E:\桌面\Resource\temp>git br
* master
#将branch指令起别名br
--------------------------------------------------------------------------------------
git config --global alias.psm 'push origin master'
git config --global alias.plm 'pull origin master'
#使用指令psm代替push origin master指令岂不是很方便？
```

## 其他配置

```bash
#设置git的默认编辑器
git config --global core.editor "vim"
#设置终端的颜色
git config --global color.ui true
#设置显示中文文件名
git config --global core.quotepath false
```

## diff指令

>
>
>diff指令用来查看对文件做了哪些改动

```bash
#直接输入git diff 比较的是当前文件和暂存区文件的差异，暂存区就是没有执行git add的文件
git diff <$id1> <$id2> #比较两次提交之间的差异
git diff <branch1> .. <branch2> #在两个分支间进行比较
git diff --staged #比较暂存区和版本库的差异
```

## checkout指令

>
>
>checkout指令不仅可以用来切换到某个分支，还可以用来切换tag，切换到某次commit

```bash
git checkout v1.0
git checkout commitid #commitid可在git log中查看
#checkout还有撤销的意思，但只能撤销还没有add进暂存区的文件
```

## stash指令

```bash
git stash
#将当前分支所有没有commit的代码暂存起来
git stash list
#暂存区会多出一条记录
git stash apply
#还原stash之前的状态
git stash drop
#删除暂存区的一条stash记录
git stash pop
#还原代码并且删除暂存区
git stash clear
#清空暂存区
```

## merge && rebase

> merge是合并分支的意思，
>
> rebase也是合并分支的意思

```bash
git merge a
#合并分支a
git rebase a
#合并分支a
#merge是直接合并，而rebase会对两个分支进行比较，按照某种顺序排序，再合并
```

## 解决冲突

>
>
>分支a与分支b对同一个文件进行了修改，当合并的时候，git无法判断哪个是正确的，所以就需要手动解决这个冲突后才能提交。

## 分支管理

```bash
git branch develop
#新建一个develop分支
#注意新建分支是基于当前所在的分支的基础上进行的，此时，develop分支与master分支的内容一模一样。
git checkout develop
#切换到develop分支
git checkout -b develop
#新建分支develop并切换到该分支
git push origin develop
#将分支develop推送到远程仓库
git push origin develop:develop2
#给远程仓库分支起别名，不建议此操作
git branch
#查看本地分支列表
git branch -r
#查看远程仓库分支
git branch -d develop
#删除分支develop
git branch -D develop
#强制删除分支develop
git push origin :develop
#删除远程分支
git checkout develop origin/develop
#远程有仓库develop而本地没有，将远程的develop分支迁移到本地
git checkout -b develop origin/develop
#远程有仓库develop而本地没有，将远程的develop分支迁移到本地，并切换到该分支
```

