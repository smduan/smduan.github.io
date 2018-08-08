---
title: Hexo+github搭建个人博客（五）：百度和谷歌收录博客
date: 2018-08-08
categories: 
- hexo搭建个人博客

tags:
- hexo
- sitemap

---
## Why
个人博客搭好之后，我们的博客并没有被百度和谷歌收录，也就是说只能自己看，别人搜不到。
<!-- more -->
在百度或者谷歌的浏览器中输入：site:duansm.top

![](/images/hexo-sitemap/site.png)

可以发现谷歌搜索不到我们的博客
## 百度收录
### 提交网址
登录[百度搜索资源平台](https://ziyuan.baidu.com/)。

![](/images/hexo-sitemap/tijiao.png)

选择网站支持->链接提交，按照提示提交自己的网址。
### 验证网站所有权
验证网站所有权的方式有三种：文件验证、html标签验证和cname解析验证。笔者使用cname解析验证。这种方法比较简单，将百度生成的主机路径和主机值添加到阿里云域名对应的cname解析中即可。

![](/images/hexo-sitemap/baidu-cname.png)

然后点击验证即可。
### 产生sitemap
我们需要使用npm自动生成网站的sitemap，然后将生成的sitemap提交到百度和其他搜索引擎。sitemap是一种文件，您可以通过该文件列出您网站上的网页，从而将您网站内容的组织架构告知Google和其他搜索引擎。Googlebot等搜索引擎网页抓取工具会读取此文件，以便更加智能地抓取您的网站。

首先安装插件：

```bash
$ npm install hexo-generator-sitemap --save
$ npm install hexo-generator-baidu-sitemap –-save
```

编辑博客配置文件:

```bash
$ vim _config.yml
# 自动生成sitemap
sitemap:
  path: sitemap.xml
baidusitemap:
  path: baidusitemap.xml
```

保存文件，重新部署博客，查看：duansm.top/sitemap.xml。显示如下信息表示sitemap生成成功。

![](/images/hexo-sitemap/sitemap-xml.png)

### 提交sitemap
在百度资源搜索平台，找到链接提交，这里我们可以看到有两种提交方式，自动提交和手动提交，自动提交又分为主动推送、自动推送和sitemap。

自动推送配置最简单，因此笔者选择自动推送。

在主题next的配置文件中进行如下修改

```bash
$ vim _config.yml
baidu_push: true
```

然后就会将./themes/next/layout/_third-party/seo/baidu-push.swig中的代码自动推送给百度。这样每次访问博客中的页面就会自动向百度提交sitemap。

百度收录网站到此配置结束。只需要等待百度收录，这个过程会比较久。

## google收录
### google search console添加博客网址
登录[google search console](https://www.google.com/webmasters/)

![](/images/hexo-sitemap/search-console.png)

添加自己的博客网站
### 验证网站所有权
google同样有多种方法。笔者选择CNAME方式验证。

![](/images/hexo-sitemap/google-cname.png)

将上图中生成的CNAME标签和CNAME目的地内容添加到阿里云解析设置中。

![](/images/hexo-sitemap/add-set.png)

然后在google search console中验证。

![](/images/hexo-sitemap/yanzheng.png)
### 提交sitemap
在google search console中选择站点地图->添加/测试站点地图

![](/images/hexo-sitemap/add-google-sitemap.png)

谷歌收录的设置到此结束。
### 验证google收录是否成功
在google浏览器中输入：site:duansm.top，查看是否能够搜到博客。

![](/images/hexo-sitemap/google-site.png)

从图中可以看出，google已经能够搜索到博客，即google收录成功。
## 参考文献
[博客提交百度和谷歌收录](https://www.jianshu.com/p/201d064e9f50)