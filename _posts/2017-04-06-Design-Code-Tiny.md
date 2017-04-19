---
layout:  post
title:   设计模式
category:  软件设计
tags:    [设计]
description:  整理的一些软件设计参考资料
---

### UML图示

![](https://img.alicdn.com/imgextra/i2/232721121/TB2kmUxmb0kpuFjy0FjXXcBbVXa_!!232721121.jpg)

### 设计模式草图

[![](https://img.alicdn.com/imgextra/i4/232721121/TB2WLFemq8lpuFjy0FpXXaGrpXa_!!232721121.jpg)


### 一些概念

1、依赖倒置原则（DIP)：一种软件架构设计的原则（抽象概念）。-------高层模块定义接口，低层模块负责实现      

2、控制反转（IoC)：一种反转流、依赖和接口的方式（DIP的具体实现方式）。       

3、依赖注入（DI)：IoC的一种实现方式，用来反转依赖。         

4、Ioc容器：依赖注入的框架，用来映射依赖。     


### Ioc容器Ninject用法

           StandardKernel kernel = new StandardKernel();//创建Ioc容器
           kernel.Bind<IDataAccess>().To<SqlServerDal>();//注册依赖
          Order order = kernel.Get<Order>();//获取目标对象
