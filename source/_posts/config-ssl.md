---
title: HTTPS——SSL配置与实现
date: 2018-05-22 18:14:18
tags:
		- SSL证书
		- HTTPS
---

#### 1、在对应的云服务器上可以申请到免费的 SSL 证书，这里以腾讯云为例说明。进入腾讯云后台，在产品列表中选择 SSL 证书，我们选择其中的域名型免费版（DV）。
<!-- more -->
![](../images/ssl1.png)  
#### 2、填写申请的信息，填写信息之后，确认申请。申请之后，可以选择自动添加解析，也可以手动添加解析，之后后台是机器审核扫描 CA 证书，时间很快，通过之后，会颁发证书，然后我们就可以去使用证书了。
![](../images/ssl2.png)

#### 3、配置网站，能够使用 HTTPS 访问，这里以 Nginx 部署为例。
##### 3.1 获取证书
我们在第二步中，可以将证书下载下来，里面有关于 Nginx 的证书。包括
	
	1_www.domain.com_bundle.crt //证书
	2_www.domain.com.key		 //私钥文件  

1__www.domain.com_bundle.crt 文件包括两段证书代码 
	
	“-----BEGIN CERTIFICATE-----”和“-----END CERTIFICATE-----”
	
2_www.domain.com.key 文件包括一段私钥代码
	
	“-----BEGIN RSA PRIVATE KEY-----”和“-----END RSA PRIVATE KEY-----”。
	
	
##### 3.2 证书安装
将域名 `www.domain.com` 的证书文件 `1_www.domain.com_bundle.crt`、私钥文件`2_www.domain.com.key`保存到同一个目录，例如`/usr/local/nginx/conf`目录下。
更新 Nginx 根目录下 `conf/nginx.conf` 文件如下：

	server {
        listen 443;
        server_name www.domain.com; #填写绑定证书的域名
        ssl on;
        ssl_certificate 1_www.domain.com_bundle.crt;
        ssl_certificate_key 2_www.domain.com.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
        ssl_prefer_server_ciphers on;
        location / {
            root   html; #站点目录
            index  index.html index.htm;
        }
    }
   
配置完成后，先用`bin/nginx –t`来测试下配置是否有误，正确无误的话，重启 nginx。就可以使 https://www.domain.com 来访问了。配置文件参数如下
![](../images/ssl3.png)

##### 4 使用全站加密，http 自动跳转 https（可选）
对于用户不知道网站可以进行 https 访问的情况下，让服务器自动把 http 的请求重定向到 https。
在服务器这边的话配置的话，可以在页面里加 js 脚本，也可以在后端程序里写重定向，当然也可以在web服务器来实现跳转。Nginx 是支持 rewrite 的（只要在编译的时候没有去掉pcre）
在 http 的 server 里增加 `rewrite ^(.*) https://$host$1 permanent`;
这样就可以实现 80 进来的请求，重定向为 https 了。