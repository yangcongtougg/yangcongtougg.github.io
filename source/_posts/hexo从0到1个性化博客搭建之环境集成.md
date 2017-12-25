---
title: Hexo从0到1个性化博客搭之环境集成
date: 2017-12-22 11:13:46
tags: Hexo
categories: Hexo
---

### 概述
> 入行有三年了，一直想拥有属于自己的博客，但我这种懒癌患者知道今天才下手。亲手配置上自己的域名，域名前绿色的小锁；高度定制的前端页面；并且我们不用担心项目的部署问题、前端静态页面的生成问题；最重要的是所有的配置需要免费（大声跟我念 **qiong**）。
网络上关于博客搭建的教程一大堆，在此仅作为我自己搭建博客的一个记录及踩过的坑；不尽详尽。ok，废话不多说，here we go。
现有如下的方案
- **Node.js** (静态HTML页面生成的底层支持)
- **Git** (版本管理，博客项目也需要部署到GithubPage上)
- ** GithubPage** (博客部署，Github免费提供1G的存储空间)
- **Hexo** (快速、简洁、高效的博客框架，为Github开源框架)
- **Hexo-theme-next** (博客主题插件，为Github上特别流行的开源框架)
- **阿里云域名服务** (添加自己的域名)
- **Cloudflare CDN分发服务** (让自己的域名支持https)
- **Hexo所支持的三方插件服务** (集成站内的全文检索等功能)
- **免费的图床** (如7牛)
- **DaoVoice** (支持站内时时聊天)
此外，只要你会**markdown**，就可以打造属于自己的个性化的博客站点。以上所有版本在此不再一一列举，保持最新版本即可。


### 环境搭建
- 安装[Node.js](https://nodejs.org/zh-cn/)
![](https://t1.aixinxi.net/o_1c1ugl7i54uvf0fkvra2l11jba.png-w.jpg)
点击下载安装即可(根据自己不同的操作系统选择)。
---
- 安装[Git](https://git-scm.com/)
![](https://t1.aixinxi.net/o_1c1uh6db42ou10gs1cpnbnm1fg7a.png-w.jpg)
选择合适的操作系统。
---
- 配置GithubPage
登陆自己的Github账户，创建仓库。
![](https://t1.aixinxi.net/o_1c1uk568c1huh10b81op4183n1m3ea.png-w.jpg)
其中红线部分换成你自己的Github名，此格式为固定格式不容许更改。在自己的桌面将项目clone下来，此时应该一个文件也没有(如果创建了README.md文件，hexo在执行hexo g 的时候会将此文件也生成为静态的html文件)。此时在本地创建两个分支master为默认的主分支，(部署我们的生成的静态文件使用)，创建一个分支列如hexo用来存放我们的hexo的项目。创建好分支之后与远程仓库进行关联push上去。
---
- 安装[Hexo](https://hexo.io/zh-cn/index.html) 
![](https://t1.aixinxi.net/o_1c1uhgdt91ts7apf1nnk1foed37a.png-w.jpg)


打开终端执行
```
npm install hexo-cli -g
```
cd 到刚才的从github clone下来的文件夹下将git checkout 到hexo分支下，然后执行
```
hexo init
```
然后执行
```
npm install
hexo server
```
然后你会看到类似这样的文件内容
![](https://t1.aixinxi.net/o_1c1uhrp9i1dmev3g5e7crdioua.png-w.jpg)
此时继续在终端执行
```
hexo clean
hexo g
hexo s
```
不出什么差错的话就可以在本地的4000端口上看到默认的博客站点，control+c可以停掉站点
默认的站点中会有一篇Hello world的文章。
可以先介绍一下部署的命令:
```
hexo d -g
```

执行此条命令之后，hexo会自动的将生成的静态html文件部署到你github项目的master分支上去；也就是会在.deploy文件中生成需要部署的静态html文件(需要配置_config.yml文件稍后讲解)；也就是说我们今后不用刻意的push项目的master分支；我们的版本控制在hexo分之下即可。我们也不用配置.gitignore文件(保持默认的忽略文件即可)。

---
- 安装hexo-theme-next
1. cd 到blog文件夹下的themes文件夹下执行
```
git clone git@github.com:iissnan/hexo-theme-next.git
```
2. 或者执行
```
git clone https://github.com/iissnan/hexo-theme-next.git
```
第一种方式需要在本地git及github上配置SSH KEY，在此不再赘述。
![](https//t1.aixinxi.net/o_1c1uil0ghjts1d7ovd177p78a.png-w.jpg)
clone完毕之后如下图


---
![](https://t1.aixinxi.net/o_1c1ul9kr91ce114i7ku417vcehoa.png-w.jpg)
我们需要在文件夹的目录下显示隐藏文件(因为有坑)；进入themes文件夹下的hexo-theme-next文件夹，将.git隐藏文件删除；如果不删除当hexo项目切换到hexo分支push的时候是不包括hexo-theme-next文件夹下的内容的(它本身也是一个git仓库)。

---
- 购买[阿里云域名服务](https://cn.aliyun.com)
笔者花了15¥/年购买了自己的域名服务，国际域名的话还是实名认证比较好。
![](https://t1.aixinxi.net/o_1c1umpavt3vv1kfp1u5o549fa.png-w.jpg)
域名的配置稍后展示。

---
- 注册[Cloudflare](https://www.cloudflare.com)做CDN分发，配置SSL
可能需要科学上网更快点
![](https://t1.aixinxi.net/o_1c1umvn7nd49tq0119prvcpnca.png-w.jpg)
所需配置稍后讲解。

---
- 注册免费的图床[7牛](https://www.qiniu.com)
![](https://t1.aixinxi.net/o_1c1un6df1lu01lkv1kvkpam12npa.png-w.jpg)
Markdown编辑所用。

---
- 注册[DaoVoice](https://account.daocloud.io/signin)站内及时消息
![](https://t1.aixinxi.net/o_1c1unbgru16ek1aetu9g1noe2lba.png-w.jpg)
最好使用github账号登录。

---
**至此现有的基本准备工作基本完成**。













































































































































