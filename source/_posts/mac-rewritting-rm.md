---
title: mac改写rm命令：移到废纸篓
date: 2018-08-05
categories: 
- mac小技巧

tags:
- mac
- trash

---
## Why
作为一个mac用户，习惯了在终端操作，删除文件也习惯上使用rm -fr命令。使用这种方式删除的文件不会出现在废纸篓中，若是误删某个重要文件，想要找回会比较复杂。
<!-- more -->
针对这个问题，一种简单的方案是替换掉rm命令，写一个脚本，将要删除的文件移动到用户下的.Trash文件夹下，也就是废纸篓，但是有一个缺点就是，无法使用废纸篓的复原文件功能。

理想的解决方案是：使用trash脚本替换rm命令，它的实质是调用finder的api进行删除操作，也就是移除到废纸篓，也就拥有了废纸篓的恢复源文件功能。
## 安装trash
[trash博客地址](http://hasseg.org/blog/post/406/trash-files-from-the-os-x-command-line/)和[trash脚本github地址](https://github.com/ali-rantakari/trash)

使用homebrew安装trash，若是没有安装homebrew且不清楚homebrew的，请参考[【工具】Homebrew的安装及使用](https://www.jianshu.com/p/4e80b42823d5)或者[官网](https://brew.sh/index_zh-cn.html)

```bash
$ brew install trash
```

此时已经可以使用trash -fr filename，命令与rm一样。但是由于已经习惯性地使用rm命令，改成trash还是有时会习惯性地使用rm删除，因此将rm替换为trash
## 使用trash替换rm命令
在~/.bash_profile文件中将rm指向trash，添加下列语句。

```bash
$ vim ~/.bash_profile
alias rm=“trash”
```
```bash
$source  ~/.bash_profile    #使添加的命令生效
```

此时使用rm命令删除文件后会发现文件在废纸篓里了，而且可以使用放回原处的功能。