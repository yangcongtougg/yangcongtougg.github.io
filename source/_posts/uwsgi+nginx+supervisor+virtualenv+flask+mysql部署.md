---
title: uwsgi+nginx+supervisor+virtualenv+flask+mysql部署
date: 2018-02-27 16:08:42
tags: flask项目部署
categories: Linux
---


> 记录一下flask项目的部署经历；大致的部署思路为uwsgi作为web服务器，nginx作为反向代理、virtualenv和virtualenvwrapper作为python的版本虚拟管理环境、supervisor作为uwsgi的进程守护者、mysql作为数据库；安装在centos7上。

## 环境搭建
#### google云服务器的root及密码登录
自己用的是google的云服务器，赠送的300$很是诱人，目前google的云服务器为了安全，默认没有开启root及密码登录功能；当然你也可以配置ssh秘钥，但是为了方便还是开启root及密码登录。
- 进入centos7之后执行`sudo -su root`切换到root权限，然后为root账户设置密码：执行`passwd root`回车，二次确认密码之后完毕。
- 之后用vim编辑sshd_config文件，执行`vim /etc/ssh/sshd_config`，进入编辑模式之后，找到PermitRootLogin和PasswordAuthentication，选项改为yes。

```
# Authentication:
LoginGraceTime 120
PermitRootLogin yes //默认为no，需要开启root用户访问改为yes
StrictModes yes
 
# Change to no to disable tunnelled clear text passwords
PasswordAuthentication yes //默认为no，改为yes开启密码登陆
```
之后执行`service shhd restart`（centos6为`/etc/init.d/ssh restart`）重启ssh服务之后，重新root登录centos即可。

#### centos 项目环境及编译环境搭建
- 安装wget执行`yum -y install wget`。
- 安装pip及依赖包执行`yum -y install pip`或者`yum -y install epel-release`和`yum -y install python-pip`。
- 安装编译环境及依赖执行`yum -y install gcc* python-devel gcc gcc-c++ httpd-tools make libtool zlib zlib-devel openssl openssl-devel pcre pcre-devel`。

#### 安装nginx
- 在/root目录下源码下载nginx1.12.0稳定版，执行`wget http://nginx.org/download/nginx-1.12.0.tar.gz`。
- 解压`tar -zxf nginx-1.12.0.tar.gz`。
- 当前用户添加nginx执行`useradd nginx`。
- 配置nginx及指定安装目录为/usr/local/webserver/nginx和安装需要的nginx模块，执行`./configure --prefix=/usr/local/webserver/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_mp4_module --with-http_flv_module --with-http_stub_status_module --with-pcre`回车检查；
之后执行`make && make install`。
- 配置软链，使在当前的用户root目录下也可以方便的执行nginx的启动和关闭，而不用再次进入到/usr/local/webserver/nginx目录下启动nginx。
执行`ln -s /usr/local/webserver/nginx/sbin/nginx /usr/sbin/`
- 进入到nginx的安装目录下找到nginx的conf文件夹进行nginx的配置，nginx.conf大致上包含了nginx.conf.default，所以一般配置nginx.conf即可。
```
# nginx的配置文件
```

- nginx启动 直接执行`nginx`。
- nginx关闭 直接执行`nginx -s stop`。
- 查看nginx的端口 执行`netstat -anptu | grep nginx`。
- 查看nginx的进程情况 执行`ps aux | grep nginx`。



