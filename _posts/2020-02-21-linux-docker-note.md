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