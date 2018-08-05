---
title: Hexo+github搭建个人博客（二）：博客个性化设计

categories: 
- hexo搭建个人博客

tags:
- hexo
- next

---
## 主题选择
hexo默认的主题是landscape，这个主题用一个字总结就是--丑。

hexo官网提供很多简洁、精美的[主题](https://hexo.io/themes/)供选择，笔者选择[next主题](https://github.com/iissnan/hexo-theme-next)。这款主题制作精美而且用户较多，遇到问题基本上能够搜到解答。
<!-- more -->

下面开始安装并做相应的个性化配置。
## 主题安装及常规设置
首先下载next主题

``` bash
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

在博客根目录（/hexo/blog/）下修改博客配置，使用next主题:

``` bash
$ vim _config.yml
 theme: next
```
将默认的landscape主题改成next

修改主题配置

``` bash
$ cd themes/next
$ vim _config.yml
#将下面的注释去掉
menu:
  home: / || home
  about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive

#设置自己的github链接和邮箱链接，有需要的自己设置，没有的不用管
social:
  GitHub: https://github.com/yourname || github
  E-Mail: mailto:yourmail@gmail.com || envelope

#设置主题风格，笔者喜欢Gemini
scheme: Gemini     
```

回到博客根目录下，执行下列命令

``` 
$ hexo new page "about"
$ hexo new page "tags"
$ hexo new page "categories"
```
上面三条命令创建关于页面、分类页面和标签页面，这三个页面默认是关闭的，根据自己的需要创建。

将主题语言设置为中文，默认为英文
``` 
$ cd /hexo/blog/themes/next/language/
$ cp zh-Hans.yml default.yml
```

重新部署博客，在浏览器中看到博客如下图所示。

![](/images/next-page-1.png)

与前面的主题相比是不是发现b格提升了许多。上面安装的主题功能比脸都干净，许多功能需要自己配置。

## 添加统计访问量功能
为博客添加统计访问量的功能。这里使用的是不蒜子算法。

在主题配置文件(themes/next/_config.yml)中修改如下信息：

``` bash
$vim _config.yml
busuanzi_count:
  # count values only if the other configs are false
  enable: true
  # custom uv span for the whole site
  site_uv: true
  site_uv_header: <i class="fa fa-user"></i> 访问人数
  site_uv_footer: 人
  # custom pv span for the whole site
  site_pv: true
  site_pv_header: <i class="fa fa-eye"></i> 总访问量
  site_pv_footer: 次
  # custom pv span for one page only
  page_pv: true
  page_pv_header: <i class="fa fa-file-o"></i> 阅读数
  page_pv_footer:
```
保存文件，重新部署博客，可以看到文章顶部显示阅读数

![](/images/count-top.png)

网页底部显示访问人数和访问量

![](/images/count-bottom.png)

## 添加评论功能
笔者使用韩国的来必力(livere)评论系统，因为其配置和使用相对简单。[官网地址](https://livere.com/)。首先根据其提示注册然后登录。

![](/images/livere-install.png)

登录之后，点击安装—>现在安装。根据提示填写信息之后生成id号。

![](/images/livere-id.png)

复制上图中的data-uid

修改主题的配置文件

``` bash
$ vim /hexo/blog/themes/next/_config.yml
livere_uid: data-uid         #data-uid就是上面生成的id号。
```

重新部署博客，可以看到评论框

![](/images/livere-talk.png)
## 添加搜索功能
当博客文章较多时，需要使用到搜索功能，搜索关键字找到目标文章。

首先安装hexo搜索相关的插件

``` bash
$ npm install hexo-generator-searchdb --save
```

启用搜索功能，在博客配置文件中加入下列配置内容

``` bash
$ vim _config.yml
search:   path: search.xml   field: post   format: html   limit: 10000
```

在主题配置文件中修改

``` bash
$ vim themes/next/_config.yml
local_search:
  enable: true
```
重新部署博客，可以使用搜索功能

![](/images/search.png)

至此一些常用的功能已经配置完成。

## 参考文献
[Hexo搭建博客系列：（一）Hexo安装与添加NexT主题](https://www.jianshu.com/p/f6bf20c1e984)

[Hexo next主题添加本地搜索 - 不使用第三方服务
](http://hisen.me/20170407-Hexo%20next%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%9C%AC%E5%9C%B0%E6%90%9C%E7%B4%A2%20-%20%E4%B8%8D%E4%BD%BF%E7%94%A8%E7%AC%AC%E4%B8%89%E6%96%B9%E6%9C%8D%E5%8A%A1/)

[创建关于页面](https://github.com/iissnan/hexo-theme-next/wiki/%E5%88%9B%E5%BB%BA-%22%E5%85%B3%E4%BA%8E%E6%88%91%22-%E9%A1%B5%E9%9D%A2)

[hexo添加评论功能](https://blog.csdn.net/ganzhilin520/article/details/79048010)

[Hexo+Next主题 文章添加阅读次数，访问量等](https://blog.csdn.net/xr469786706/article/details/78166227)


