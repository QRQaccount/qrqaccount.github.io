---
title: 使用github组织项目
date: 2020-08-13 15:16:35
tags:
- git
categories:
- 开发工具
---

# 使用github组织项目

更为详细的说明参考[GitHub Docs](https://docs.github.com/cn)

## 仓库

在项目的开始到结束，我们会有两种仓库。一种是源仓库（origin），一种是开发者仓库,开发者仓库由源仓库fork而来

### 源仓库

源仓库仅有项目管理员对其操作<br>源仓库起到的作用:

- 汇总各个开发者的代码
- 存放趋于稳定和可发布的代码

### 开发者仓库

开发者仓库独立于源仓库，由源仓库fork而来。开发者在自己的仓库完成开发工作后源仓库发送`pull request`，请求合并自己的代码

## 分支

在开发项目时有两类分支,永久分支和临时分支

### 永久分支

- `master`分支:用于存放经过测试的稳定的代码，每一次`master`更新都代表新的版本发布
- `devlopment`分支:从master分支分离出来，用于存放基本稳定的代码，即开发分支

### 临时分支

- `feature`分支:特性分支。用于项目开发功能
- `hotfix`分支:若产品已经发布,发现紧急bug通过此分支修改后同步到`master`分支和`devlopment`分支里 
- `release`分支

## 从创建到完工

### 第一步

由项目的管理者创建一个源仓库，以xxx\abc为例。并初始化两个永久性分支`master`和`devlopment`

### 第二步

从源仓库fork到自己的账户中

### 第三步

从自己的仓库中clone到本地

### 第四步

构建新的`feature`分支。以添加一个"Add"功能为例

- 首先切换到`dev`分支

  ```bash
  git checkout devlop
  ```

- 创建相应`feature`分支

  ```bash
  git checkout -b feature-add
  ```

- 进行开发

- 提交更改

  ```bash
  git add .
  git commit -m "finish add feature"
  ```

- 切换回`dev`分支

  ```bash
  git checkout devlop
  ```

- 将功能合并到`dev`分支

  ```bash
  git merge --no-ff feature-add
  ```

- 删除`feature`分支

  ```bash
  git branch -d feature-add
  ```

- 将本地修改提交自己的仓库

### 第五步

在完成自身`dev`分支的开发后，可以请求管理员将自己的仓库的`dev`分支合并到源仓库的`dev`分支中

### 第六步

管理员测试，合并

- 对代码review
- 创建测试分支进行测试

如果没有问题就将代码合并到源仓库的`dev`分支

![](git-model@2x.png)

<center>图片来源https://github.com/livoras/blog/issues/7</center>