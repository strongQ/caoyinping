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
