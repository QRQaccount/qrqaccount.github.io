---
title: git的常用方法总结
date: 2020-08-06 16:06:38
tags:
- git
categories:
- 开发工具
---

# git的常用场景总结

## 初始化项目并提交

1. `git init` 初始化项目

2. `git add .` 添加文件/目录

3. `git commit -m "描述"` 添加到本地版本库

4. `git remote add [别名] [远程仓库地址]` 将git推送到远程版本库

5. `git push [远程主机名] [本地分支名]:[远程分支名]` 将本地分支的git推送到指定远程主机的指定分支 
   
   - `git push -u [指定默认主机名] [本地分支名]:[远程分支名]`这样可以添加一个默认的主机，在之后的`git push`中可以不再使用参数
     
     <!-- more -->

## 分支代码合并

### 使用merge

以将分支dev代码合并到master分支为例

1. `git branch` 查看当前分支
2. `git check out master` 切换到master分支
3. `git pull [远程主机名] master` 将远程master分支pull下来
4. `git merge dev` 将dev分支代码合并到master上
5. `git status` 查看是否有冲突
6. `git push [远程主机名] master` 提交master代码
7. `git checkout dev` 切换回dev分支进行开发
   - `git checkout [hash]` 切换回指定历史版本 

### 使用rebase

以master分支经过一次hotfix后将master分支合并到dev为例

> **注意!!!**:在公共分支上最好不要使用rebase这将修改提交历史，对多人协作可能造成影响

1. `git branch` 查看当前分支
2. `git check out dev` 切换到dev分支
3. `git rebase master` 
4. 如果有冲突，解决之后根据提示使用`git add .`保存更改，但是不要提交，而是使用`git rebase --continue`，这时候commit ID又会回到dev分支

## 删除分支

### 删除本地分支

- `git branch -d [分支名称]` -d即--delete删除分支时,该分支必须完全和它的上游分支完成merge,如果没有上游分支,必须要和HEAD完全merge
- `git branch -D [分支名称]` --delete --force可以在不检查merge状态的情况下删除分支
- `git branch -f [分支名称]` --force将当前branch重置到初始状态

### 删除远程分支

`git push origin --delete branch` 会一同删除追踪分支

### 删除追踪分支

`git branch --delete --remotes <remote>/<branch>` 可以删除追踪分支,该操作并没有真正删除远程分支,而是删除的本地分支和远程分支的关联关系

### 删除已经合并到master的分支

`git branch --merged master | grep -v '^[ *]*master$' | xargs git branch -d`<br>*原理：* 

1. `git branch --merged master` 列出所有已经合并到master的分支
2. `grep -v '^[ *]*master$'` 在其中排除master分支
3. `xargs git branch -d` 删除分支
