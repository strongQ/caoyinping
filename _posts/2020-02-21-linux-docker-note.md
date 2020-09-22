---

layout:        post
title:         docker操作整理
description:   整理部分docker知识
keywords:      docker
categories:    [linux]
tags:          [linux,docker]

---

----------------------------

### 1、mysql数据的导入导出

  1、导出数据库

     docker exec -it mysql的容器名字 mysqldump -uroot -proot 数据库名字 > /导出文件名.sql  


  2、导入

    // 文件复制到容器
    docker cp /文件名.sql 容器名:文件名.sql
    // 进入容器
    docker exec -it mysql容器名 bash
    // 进入mysql
    mysql -uroot -p
    // 导入
    source 文件名.sql

### 2、mysql Docker启动   

    // 拉取镜像
     docker pull mysql:5.7    
    // 启动容器
     docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=3458888 -e MYSQL_ROOT_HOST=% mysql:1.0 --character-set-server=utf8 --lower-case-table-names=1


### 3、mysql8.0修改root密码


      mysql> ALTER USER'root'@'%'IDENTIFIED WITH    mysql_native_password BY 'password';
      mysql> FLUSHPRIVILEGES;

### 4、mysql8.0设置大小写不敏感

     sudo dpkg-reconfigure mysql-server-8.0

   