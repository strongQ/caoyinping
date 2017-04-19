---
layout:  post
title:    分布式数据采集系统
category : 项目
tags:     [服务，网络，脚本]
description:
---


### 项目介绍
  该系统功能不复杂，但是内部的一些模块还是具有一定的价值的。
  一共分为3个部分：     

  1、Brain 控制中心   
  控制所有采集器，收集采集器配置信息，动态配置节点下的采集器。       
  2、DataCollectorUnit 采集器   
  负责采集工业数据，并且和Brain进行通信。      
  3、ModuleSetting 采集器配置界面     
  对采集器信息进行客户端配置


### 主要功能介绍
#### Brain
  利用NetworkComms通知采集器重启


    NetworkComms.SendObject<string>("TransRestart",xx,xx,"Close");
  采集器接收


    NetworkCOmms。AppendGlobalIncomingPacketHandler<string>("TransRestart",(packetHeader,connection,input)=>{ Application.Restart(); Process.GetCurrentProcess().Kill();});


#### DataCollectorUnit
该部件是系统的核心，包含4种服务，顺序启动服务：


     // 协议模块服务
    await Run(moduleService,contentProvide);
    // 数据模拟服务
    await Run(simService,contentProvide);
    // 公式解析服务
    await Run(expService,contentProvide);
    // 远程连接服务
    await Run(remoteService,contentProvide);




1、moduleService的功能主要是获取modbus设备和模拟modbus设备，模拟modbus设备是通过软件来模拟电表生成应答帧！因此当该服务启动后，设备可以连接到模拟设备和真实设备上去，而模拟设备的数据需要simService来提供。


    public async System.Threading.Tasks.Task PreRun(IContentProvide iContent)
      {
          ExtendMethod.Log.Info("开始启动模块服务！");
          IDictionary<string, int> moduleDic = iContent.GetModuleMsg();       
          foreach (var name in moduleDic.Keys)
          {
              DeviceProvide dp=new DeviceProvide(name+".db");
              // 表在哪个端口下
              ModbusChannel modbusChannel = new ModbusChannel() { Port = moduleDic[name] };
              SIMChannel simChannel = new SIMChannel();
              await modbusChannel.PreRun(dp);
              await modbusChannel.Run();
              await simChannel.PreRun(dp);
              await simChannel.Run();




              // 在这里加其他的模块




          }
      }




  2、数据模拟通过解析Lua脚本
  3、公式解析同样通过解析Lua


      var a=_LuaEngine.GetFunction("Expression").call(pvar.FreshValue.Value);
