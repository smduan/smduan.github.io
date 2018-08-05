---
title: Hexo+github搭建个人博客（三）：Travis CI持续集成,自动部署博客

categories: 
- hexo搭建个人博客

tags:
- hexo
- Travis CI

---
## Why
{% post_link hexo-deploy  %}所讲的博客部署方式是：

首先，本地写好文章。

然后，使用hexo g d命令部署到github上去。
<!-- more -->

但是，这种方式有个缺点是，我要在写文章的机器上安装hexo和Node.js环境。如果我们出差或者换电脑了，想要写博客，就需要重新安装博客环境。这就有点繁琐了。

是否存在一种方式，在自己电脑上只写博客，然后github上的博客源码会自动编译部署？这样我们机器上就不需要安装hexo和Node.js环境，可以随时随地更新博客。

这种方式是存在的。使用[Travis CI持续集成](https://travis-ci.com/)。Travis CI 是目前新兴的开源持续集成构建项目，它与jenkins，GO的很明显的区别在于采用yaml格式，同时他是在线服务，不像jenkins需要本地搭建服务器。目前大多数的github项目都已经移入到Travis CI的构建队列中。

主要做法是：首先将博客源码托管到github，最好是托管到部署博客的仓库的一个分支上，让Travis检测该分支的push变动，只要有push操作，即对博客进行重新部署。这样的话，我们在本地写完博客，将博客push到github上，Travis CI会自动构建环境并进行博客部署。

## github配置
### 托管主题
在github上新建一个空的仓库，命名为next用来托管{% post_link hexo-next  %}中配置好的主题。

``` bash
$ git clone https://github.com/yourname/next
$ cp -r /hexo/blog/themes/next/* ./next/
$ git add .
$ git commit -m 'add themes'
$ git push origin master 
```
这样就将配置好的主题托管到github了，travis在构建环境的时候下载配置好的主题即可。

### 托管博客源码
在github部署博客网页的仓库中新建一个blog-source的空分支，将博客源码托管到这个分支即可。

``` bash
$ git clone -b blog-source https://github.com/yourname/yourname.github.io
$ cp -r /hexo/blog/* ./yourname.github.io/
$ git add .
$ git commit -m 'add blog source'
$ git push origin blog-source 
```
Travis CI监控到这个分支有push操作，就会重新部署博客。

### 产生personal access token
使用travis自动部署博客，就需要travis对github仓库进行自动读写。github提供了token机制来供外部访问你的仓库。

登录github账号，选择setting—>Developer setting->Personal access token->Generate new token。

设置访问权限。根据实际情况设置。

![](/images/set-permission.png)

生成token

![](/images/Personal_access_token.png)

记住上面的token，因为只生成一次，页面关闭就没了。

## 配置Travis CI
### 配置Travis官网
因为travis是在线服务，需要在其官网进行相关配置。使用github账号登录[Travis CI官网](https://travis-ci.com/)。可以发现当前github账户中的仓库，选择要部署的仓库，然后点击setting。

![](/images/select-repo.png)

填入github生成的personal access token，并选择该仓库需要travis监控的分支。

![](/images/set-travis.png)

上面的github_token字段对应的value就是github网站上生成的personal access token。该字段的name可以随便起，但是必须与下面的travis配置文件中的对应。选择要编译的源码在blog-source分支。
### 配置.travis.yml
travis会检测.travis.yml文件并根据其中的配置项进行博客部署。在博客根目录(/smduan.github.io/)下新建.travis.yml文件，并填入以下命令。

``` bash
$vim .travis.yml
# 指定构建环境是Node.js，当前版本是稳定版
anguage: node_js
node_js: stable

# 设置钩子只检测blog-source分支的push变动
branches:
  only:
    - blog-source

# 设置缓存文件
cache:
  directories:
    - node_modules

#在构建之前安装hexo环境和主题，这里的主题就是原来修改过的主题，我将其托管到另一个github仓库，直接clone就行，否则每次都是新的主题，要重新设置。
before_install:
  - npm install -g hexo-cli
  - git clone https://github.com/smduan/next.git themes/next

#安装git插件和搜索功能插件
install:
  - npm install
  - npm install hexo-deployer-git --save
  - npm install hexo-generator-searchdb --save

# 执行清缓存，生成网页操作
script:
  - hexo clean
  - hexo generate

# 设置git提交名，邮箱；替换真实token到_config.yml文件，最后depoy部署
after_script:
  - git config user.name "smduan"
  - git config user.email "shaomingduan@gmail.com"
  # 替换同目录下的_config.yml文件中github_token字符串为travis后台刚才配置的变量，注>意此处sed命令用了双引号。单引号无效！
  - sed -i "s/github_token/${github_token}/g" ./_config.yml
  - hexo deploy
```

保存文件，并将该文件push到blog-source分支。

然后观察travis网页，可以发现正在编译，下图是编译成功的截图

![](/images/run-travis.png)

可以点击job log查看编译部署的日志。以后只要往blog-source分支push内容都会触发travis编译。

## 总结
此后可以在另一台没有hexo和node.js环境的机器上写博客并更行。步骤如下：

1、将blog-source分支拉取到本地。

2、书写博客，然后将博客文件拷贝到/source/_post/目录下

3、将更新的博客push回github的blog-source分支。travis会检测到push变动，自动更新博客。
## 参考文献
[Hexo遇上Travis-CI：可能是最通俗易懂的自动发布博客图文教程](https://juejin.im/post/5a1fa30c6fb9a045263b5d2a)

[基于 Hexo 的全自动博客构建部署系统](http://kchen.cc/2016/11/12/hexo-instructions/)

[Travis-ci自动编译部署github上的项目](https://troyyang.com/2017/06/24/Travis_Auto_Build_Deploy_Github_Projects/)

[手把手教你使用Travis CI自动部署你的Hexo博客到Github上](https://www.jianshu.com/p/e22c13d85659)
