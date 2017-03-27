---
layout:    post
title:     networkcomms和windows service用法
keywords:  service
tags:      [service]
---


### 创建Windows服务注意事项

#### 1、OnStart中的代码不能阻塞，如果有后台while循环或者耗时代码，一定要放在线程中。

#### 2、调试windows service时在OnStart方法中加入:
<pre>      Debugger.Launch()</pre>

#### 3、一定要记得在服务界面上点右键添加安装程序。

#### 4、编写bat卸载服务。
<pre>C:\Windows\Microsoft.NET\Framework\v4.0.30319\InstallUtil.exe -u TransFormService.exe
pause
</pre>

### 该项目中用到的一些方法
#### 1、为了获取一些配置信息，配置客户端必须获取服务的安装路径
<pre>

       public static string GetWindowsServiceInstallPath(string ServiceName)
       {
           string key = @"SYSTEM\CurrentControlSet\Services\" + ServiceName;
           string path = Registry.LocalMachine.OpenSubKey(key).GetValue("ImagePath").ToString();
           //替换掉双引号   
           path = path.Replace("\"", string.Empty);
           FileInfo fi = new FileInfo(path);
           return fi.Directory.ToString();
       }
</pre>

####  2、服务运行时需要补全漏掉的数据后才按照周期转移数据。
<pre>

      private void ExcuteTrans(PrepareStore ps)
      {
          var t1 = ps.CreateStoreTask();
           t1.Start();
           if (t1 != null)
           {
               t1.ContinueWith(x =>
               {
                   try
                   {
                       DateTime beginTime = x.Result;
                       LogHelper.WriteLog(string.Format("请耐心等候到 {0} 进行同步", beginTime), LogHelper.LogEnum.提示);

                       while (true)
                       {                           
                           // beginTime
                           if (DateTime.Now >= beginTime)                        
                           {
                               //存储
                               StoreData sd = new StoreData();
                               sd.Run(FrmBLL.storeTimer.CycleMinute);
                               break;                            
                           }
                          Thread.Sleep(1000);
                       }
                   }
                   catch (Exception ee)
                   {
                       LogHelper.WriteLog(ee.Message, LogHelper.LogEnum.错误);                     
                   }
               }, TaskContinuationOptions.NotOnFaulted);
           }
       }
</pre>

#### NetWorkComms创建Tcp服务

         if      (Connection.ExistingLocalListenEndPoints(ConnectionType.TCP).Count == 0)
           {
               FrmBLL.GetPort();
               dbTramsform.LogHelper.TcpPort = int.Parse(FrmBLL.Port);
               NetworkComms.AppendGlobalIncomingPacketHandler<string>("TransLog", (packetHeader, connection, input) =>
               {
                   Log = input;
               }
                   );
               Connection.StartListening(ConnectionType.TCP, new System.Net.IPEndPoint(System.Net.IPAddress.Parse("127.0.0.1"), int.Parse(FrmBLL.Port)));
           }

#### NetWorkComms客户端发送数据
    NetworkComms.SendObject("TransLog", "127.0.0.1", TcpPort, pLog);
