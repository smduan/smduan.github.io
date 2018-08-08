---
title: Mac上安装Sublime Text3和LaTex环境
date: 2018-08-08
categories: 
- Sublime Text3

tags:
- Sublime
- LaTex

---
## 安装Sublime Text3和Package Control
去[sublime官网](http://www.sublimetext.com/)下载sublime，然后点击安装。
<!-- more -->
前往[Package control官网](https://packagecontrol.io/installation)，复制下图中的代码。
![](/images/sublime-latex/package-control.png)

打开Sublime，选择View->show Console，打开控制面板。将上图中的代码复制到打开的控制面板中，点击回车，等待安装结束，然后重新启动Sublime Text。
## 安装MacTex
首先去[MacTex官网](http://www.tug.org/mactex/mactex-download.html)下载MacTex.kpg。文件3G多。

点击安装。
## 安装LatexTools
打开sublime Text。选择Sublime Text->Preferences->Package Control或者按下“Command+Shift+P”打开命令托盘Command pallet。

在命令托盘里输入“Install Package”，按下Enter回车建。

完成之后，输入“LaTeX Tools”，找到这一项并回车安装。

安装结束后重启Sublime Text
## 安装skim
前往[skim官网](https://skim-app.sourceforge.io/)下载skim并安装。

打开skim。在菜单栏中Skim > Preference(选项) > Sync(同步)

在预设菜单中选择Sublime Text。

![](/images/sublime-latex/sublime-skim.png)

关闭上面窗口。至此环境安装完毕
## 测试
打开Sublime Text3，Command+N新建文件并在里面输入如下代码。

```
%!TEX program = xelatex 
\documentclass{article} 
\usepackage{fontspec, xunicode, xltxtra} 
\setmainfont{Hiragino Sans GB} 
\title{Title} \author{name} 
\begin{document} 
\maketitle{title} 
\section{Introduction} This is where you will write your content. 在这里写上内容。 \end{document}
```
Command+S保存文件，并命名为name.tex，Command+B编译并运行，这时就可以在Skim里面看到PDF预览了。
## 参考文献
[Mac下配置sublime实现LaTeX](https://blog.csdn.net/qq_25491201/article/details/78138472)