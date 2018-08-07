---
title: Hexo+github搭建个人博客（三）：博客域名解析
date: 2018-08-07
categories: 
- hexo搭建个人博客

tags:
- hexo
- DNS

---
## Why
{% post_link hexo-deploy  %}中所讲的访问博客
地址是：https://yourname.github.io
<!-- more -->
使用github提供的二级域名看起来很别扭。希望博客有自己的域名，这样看起来也更加正式。
## 域名购买与解析
笔者在阿里云上购买一个.top域名，域名为:duansm.top。购买流程按照阿里云的提示完成购买即可。

购买域名之后，进入阿里云官网的控制台，在域名列表中可查看自己购买的域名：

![](/images/hexo-dns/aliyun-controller.png)

点击解析，然后添加记录

![](/images/hexo-dns/add-record.png)

填写信息

![](/images/hexo-dns/add-message.png)

如图一次输入：CNAME、@、Github博客域名。选择保存完成个人域名向个人博客的映射。点击确定。

![](/images/hexo-dns/dns-list.png)
### 设置CNAME文件
在博客源码的source目录下，生成CNAME文件，并填入域名。

```bash
$ vim CNAME
 duansm.top
```
保存，然后push到blog-source分支。travis ci重新部署之后。进入部署博客的github仓库，点击setting。

![](/images/hexo-dns/github-setting.png)

查看custom domain变成了阿里云购买的域名。

![](/images/hexo-dns/custom-domain.png)

至此可以是用新的域名访问博客。
## 参考文献
[个性化博客——域名绑定](https://www.jianshu.com/p/cea41e5c9b2a)

[github怎么绑定自己的域名？](https://www.zhihu.com/question/31377141)