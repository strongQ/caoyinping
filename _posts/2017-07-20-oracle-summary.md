---
layout: post
title:  oracle数据库语法整理
category: 数据库
tags:   [Oracle]
description: 工作中需要的oracle语句
---


### oracle创建用户及赋权


1、创建表空间

    create tablespace hxzg_data

     logging

     datafile 'C:\app\data\hxzg_data.dbf'

     size 50m

     autoextend on

     next 50m maxsize 20480m ;

2、创建用户

     create user db_hxzg identified by 123

     default tablespace hxzg_data;

3，复权

    grant resource,connect to db_hxzg;

4,给其他用户访问权限（db_hxzg以DBA权限登录）

     grant select any table to sun;



4、创建用户

    create user sun identified by 123;        //创建用户

    alert user sun identified by tiger;      //修改密码

5、删除用户

    drop user sun cascade;            //删除用户及对象

    drop user sun;                        //删除用户

6、授权

1、默认的普通用户sun默认未解锁，不能进行那个使用，新建的用户也没有任何权限，必须授予权限

    grant create session to sun;            //授予sun用户创建session的权限，即登陆权限
    grant unlimited tablespace to sun;  //授予sun用户使用表空间的权限
    grant create table to sun;              //授予创建表的权限
    grant drop any table to sun;                //授予删除表的权限
    grant insert any table to sun;               //插入表的权限
    grant update any table to sun;             //修改表的权限
    grant all to public;                         //这条比较重要，授予所有权限(all)给所有用户(public)

2、oralce对权限管理比较严谨，普通用户之间也是默认不能互相访问的，需要互相授权


    grant select on tablename to sun;//授予sun用户查看指定表的权限
    grant drop on tablename to sun;//授予删除表的权限
    grant insert on tablename to sun;//授予插入的权限
    grant update on tablename to sun;//授予修改表的权限
    grant update(id) on tablename to sun;//授予对指定表特定字段的插入和修改权限，注意，只能是insert和update
    grant alert all table to sun;//授予sun用户alert任意表的权限


3、查看权限

    select * from user_sys_privs;//查看当前用户所有权限
    select * from user_tab_privs;//查看所用用户对表的权限

4、权限传递

   即用户A将权限授予B，B可以将操作的权限再授予C，命令如下：

    grant alert table on tablename to sun with admin option;//关键字 with admin option
    grant alert table on tablename to sun with grant option;//关键字 with grant option效果和admin类似

5、角色

　　角色即权限的集合，可以把一个角色授予给用户

    create role myrole;//创建角色

    grant create session to myrole;//将创建session的权限授予myrole
    grant myrole to sun;//授予sun用户myrole的角色
    drop role myrole;删除角色



### 整理

1、打印信息

    set serveroutput on;  --没这句话，看不到dmbs_output信息
    dbms_output.putline('fdsdf');


2、游标

  1、      

      FOR i IN 1..10 LOOP  --noted here
      IF MOD(i,2) = 0 THEN     -- i is even
          dbms_output.put_line( 'i: '||i||' is even ' );
      ELSE
          dbms_output.put_line('i: '|| i||' is odd' );
      END IF;
      x := x + 100;
      dbms_output.put_line('x value: '|| x);
     END LOOP;
     COMMIT;


 2、

      while i < 10 loop
            begin
                   i:= i+1;
           end;
          end loop;


3、

    set serveroutput on;
    DECLARE
      c_salary customer.salary%TYPE;
      c_name customer.name%TYPE;
       CURSOR c1 is
      select name,salary from customer;
     BEGIN
      for f in c1
      loop
      dbms_output.put_line(f.name||' '||f.salary);
     end loop;
    END;



3、导出

    expdp hczz_web/password@localhost/orcl dumpfile=hczz_web_20170619.dmp
