---
title: Hexo+github搭建个人博客（一）

categories: 
- hexo搭建个人博客

tags:
- hexo

---
## 框架选择
目前较为流行的个人博客开源框架是hexo和wordpress：

wordpress： PHP动态框架，需要一台服务器，完全傻瓜式操作，技术小白可以选用。缺点很明显，需要服务器（要钱），而且更换服务器的时候数据库迁移，很麻烦。
<!-- more -->
hexo： 一款基于Node.js的静态博客框架，hexo可以部署在自己的服务器上，同时还可以方便的生成静态网页托管在全球最大同性交友网站--github（相信这是很多人选择hexo的主要原因）。完全免费，需要一定技术基础，喜欢捯饬的人可以选用。

笔者调研两款框架后直接选择hexo，不为什么。下面开始从零搭建网站。

## 1 本地部署hexo
hexo安装需要Node.js和git。在此仅提供Node.js的安装命令。

安装Node.js和hexo

``` bash
$wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh
$nvm nstall stable
$npm install –g hexo-cli
```
安装完成之后新建一个存放博客源码的目录。

``` bash
$mkdir hexo
$cd /hexo
$hexo init blog       #blog即为博客源码安装目录，名字随便起
$cd blog
$hexo server
```
![](/images/hexo_server.png)

出现如上内容，代表博客构建成功，在浏览器中输入地址:http://localhost:4000，就可以看到博客页面。

![](/images/hexo_index.png)

每次写完博客文章之后将其放到/hexo/blog/source/_posts/目录下，hexo会自动检测到该文章，无需重启服务。

## 2 github部署
上面的内容是将静态页面部署到本地服务器。但是我不想花钱买服务器。然而放在自己电脑上又不方便。不用担心，hexo支持将静态网页部署到github上。

首先在github上新建一个空的仓库，命名为:yourname.github.io。因为github提供一个二级域名，也就是上面这个域名供你访问。

可以设置[git免密认证](https://www.jianshu.com/p/b5ec092fc1d1)

在本地为hexo安装git插件

``` bash
$ npm install hexo-deployer-git  --save
```

修改配置文件，打开文件/hexo/blog/_config.yaml，在最后面添加如下内容：

``` bash
$ vim _config.yml
deploy:
  		type: git
  		repo: https://github.com/yourname/yourname.github.io.git
  		branch: master
  		message:
```

其中type为git，repo即为上面创建的github仓库地址，branch默认为master分支。

保存配置，在根目录(/hexo/blog/)下键入：

``` bash
$ cd /hexo/blog
$hexo generate    #hexo g
$hexo deploy      #hexo d
```

至此，已经将博客部署到github上，在浏览器输入:https://yourname.github.io，即可看到博客。

## 最后

至此，每次写完博客，将其复制到/hexo/blog/source/_posts/目录下，然后执行下列命令部署到github上：

``` bash
$ cd /hexo/blog
$hexo clean
$hexo g
$hexo d
```

然后登录https://yourname.github.io进行查看自己的新博客。