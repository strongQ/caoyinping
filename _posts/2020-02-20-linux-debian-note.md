---

layout:        post
title:         linux整理
description:   整理部分linux知识
keywords:      linux
categories:    [linux]
tags:          [linux]

---

----------------------------

### 1、debian安装配置

    1、设置源

    deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free    
    
    deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free  

    deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free    

    deb https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free  

   2、xrdp端口更改

    sed -i 's/port=3389/port=3390/g' /etc/xrdp/xrdp.ini

   3、设置中文

    #dpkg-reconfigure locales

    选择
    zh_CN GB2312

    zh_CN.GBK GBK

    zh_CN.UTF-8 UTF-8

    安装中文字体
    #apt-get install xfonts-intl-chinese
    #apt-get install xfonts-intl-chinese wqy*

   4、安装xfce4界面

    sudo apt-get install xorg slim xfce4

   5、安装docker

    1、添加https包
     sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg2
    2、GPG密钥
     curl -fsSL https://download.docker.com/linux/debian/gpg |  sudo apt-key add -
    3、加入国内docker源
     add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/debian $(lsb_release -cs) stable"
    4、安装
    sudo apt install docker-ce
    5、验证
    sudo systemctl status docker



### 2、ubuntu安装配置

    1、设置源
       deb-src http://archive.ubuntu.com/ubuntu xenial main restricted #Added by software-properties
       deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted
       deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties
       deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted
       deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties
       deb http://mirrors.aliyun.com/ubuntu/ xenial universe
       deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
       deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse
       deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse
       deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
       deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties
       deb http://archive.canonical.com/ubuntu xenial partner
       deb-src http://archive.canonical.com/ubuntu xenial partner
       deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted
       deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties
       deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
       deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse

    2、安装docker
    
      1、sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
      2、curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
      3、sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
      4、apt-get install docker-ce docker-ce-cli containerd.io
