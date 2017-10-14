---
title: ssh_keygen
date: 2017-10-14 22:05:21
tags: linux 
---

# 1、在A机器上生成密钥对
    ssh-keygen 一路回车或者 ssh-keygen -t rsa 一路回车

# 2、把A的公钥复制到B上
	cat .ssh/id_rsa.pub，然后复制内容到B机器上的.ssh/authorized_keys

<!-- more -->

# 3、A上直接 ssh B机器的IP