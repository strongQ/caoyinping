---

layout:        post
title:         docker
description:   something memory
keywords:      web
categories:    [web,docker]
tags:          [web,docker]

---

----------------------------

### 1、docker中部署mysql

    (1)、docker pull mysql
    (2)、启动我们的mysql的镜像，创建一个MySQL的容器
        使用命令：docker run -d --name mysql -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql

       -d表示在后台运行，不随当前命令行窗口的退出而退出
       --name给容器起一个别名，以后可以通过这个别名管理此容器
       -p 3307：3307把宿主机的3307端口映射到Mysql容器的3306端口
       -e MySQL容器的环境配置MYSQL_ROOT_PASSWORD=123456   指定mysql的密码，用户名默认为root，注意如果没有指定密码，会启动失败。
    （3）、查看运行的容器 docker ps
     (4)、查看容器IP,直接用localhost就行了，容器ip不能连接
       docker inspect --format='{{.NetworkSettings.IPAddress}}' mysql
    （5）、解决验证出错问题
       docker exec -it mysql bash
       mysql -uroot -p  填写密码进入
       授权：
       mysql> GRANT ALL ON *.* TO 'root'@'%';
       刷新权限：
       mysql> flush privileges;
       更新加密规则：
       mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
       更新root用户密码：
       mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
       刷新权限：
       mysql> flush privileges;
    （6）、挂载主机目录
    docker run -d --name mysql -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 -v C:\Users\zhangqi:/var/lib/mysql mysql

### 2、docker中部署sqlserver

    1、 docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=Zhangqi@Passw0rd' -p 1433:1433 --name sqlserver2019 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2019-GA-ubuntu-16.04
