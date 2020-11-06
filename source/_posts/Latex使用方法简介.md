---
title: Latex使用方法简介
date: 2020-09-08 16:27:42
tags:
- Latex
categories:
- Latex
---

# Latex使用方法简介

## Latex简介

Latex是一种基于Tex的排版系统。作者使用章（chapter）、节（section）、表（table）、图（figure）等简单的概念指定文档的逻辑结构，并让LaTeX系统负责这些结构的格式和布局。几乎不用手动对文章布局进行修改

## 个人开始学习Latex的契机

在Linux系统上需要一个强大，简洁，无兼容性问题的通用排版工具。虽然Markdown已经满足日常笔记的需求，但仍然无法满足复杂的排版需求，比如论文。

<!-- more -->

## Latex环境的安装

### 安装TexLive

针对于linux系统，常见的发行版的中央软件库中都会有相应的整合包，在使用国内镜像后(如阿里云，清华)直接下载就可以。对于空间较为富裕的，尽量选择全部安装,可以避免日后因为字体、宏等内容的缺失而头疼。以ubuntu系统为例

```bash
    sudo apt install texlive-full
```

整个大小大约5个G

### 安装Latex编辑器

列举一下我觉得好用的编辑器

- TexSutdio 
- TexMaker (我自己现在在用)
- VScode+Latex WorkShop插件

以及永远强大的两个编辑器

- Vim
- Emacs

## Latex入门

个人认为这类工具在初级使用阶段没必要弄清楚各种细节。在知道基本结构后直接使用，并在之后有功能需求时即使查阅资料就行。熟能生巧。
> 当然如果你的目标是精通亦或是准备为这类排版工具开发软件那请好好学习每一个细节

### 一个简单的入门例子

如所有编程语言的入门教程一样，我们编写一个显示hello world的文档。学习Latex就要有学习一门编程语言的准备。

```latex

\documentclass{article}
\begin{document}

    Hello world!!!!

\end{document}

```

输入这些之后将它保存为`.tex`文件，然后使用Latex引擎编译后(一般工具默认输出为PDF格式)，就可以查看了

对于这个简短的例子可以看出Latex文档的基本组成部分。
- `documentclass[]{}` 其中`{}`的内容用来指定文章类型。`[]`中的内容用来指定文章属性如字体大小，页面大小。这些属性必须被逗号隔开。在这里我们将文章设置为`article`。它一般用于科学期刊、 演示文档、 短报告、 程序文档、 邀请函等，也是最为常见的类型。这里我们并没有设置`[]`。latex引擎将使用默认设置。
- latex文档中的所用正文内容都在`\begin{document}`与`\end{document}`之间

### 对于中文的支持

我目前了解到的比较好的对中文的支持的方式是通过Xelatex引擎和xeCJK宏来实现

将自己的Latex编辑器的编译引擎换为Xelatex，再在头部引入xeCJK宏就可以了

```latex

\documentclass{article}
\usepackage{xeCJK}
\begin{document}
	这是一份中文文档
\end{document}

```

## 看到的比较好的教材

[latex入门-简版-刘海洋](latex入门-简版-刘海洋.pdf)
