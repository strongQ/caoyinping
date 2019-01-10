---

layout:        post
title:         wcf整理
description:   整理部分wcf知识
keywords:      wcf
categories:    [wcf]
tags:          [wcf]

---

----------------------------


### 1、客户端调用方法
     public async Task<List<string>> GetTemplates(string addr)
     {
        IClientChannel channel = null;
        var result = await WcfFactoryController.Current.GetFactory<IClientCallServer>(addr, ConnectionEnum.http);
        if (result == null) return new List<string>();
        try
        {
            var iServer = result.CreateChannel();
            channel = (IClientChannel)iServer;
            var datas = await iServer.GetTemplates();
            return datas;
        }
        catch (Exception e) { return new List<string>(); }
        finally
        {
            if (channel != null) channel.Close();
        }
      }

      // 汇聚通道
      public async Task<ChannelFactory<T>> GetFactory<T>(string address, ConnectionEnum connectionEnum, object context = null)
       {        
           return await Task.Run<ChannelFactory<T>>(() =>
           {
               try
               {

                   ChannelFactory<T> myfactory = null;
                   switch (connectionEnum)
                   {
                       case ConnectionEnum.msmq:
                           NetMsmqBinding binding = new NetMsmqBinding();
                           // 设定非事务
                           binding.ExactlyOnce = false;
                           binding.Security = new NetMsmqSecurity() { Mode = NetMsmqSecurityMode.None };
                           binding.QueueTransferProtocol = QueueTransferProtocol.Native;
                           myfactory = new ChannelFactory<T>(binding, new EndpointAddress(address));
                           break;
                       case ConnectionEnum.http:

                           BasicHttpBinding httpbinding = new BasicHttpBinding();
                           httpbinding.MaxBufferSize = 2147483647;
                           httpbinding.MaxBufferPoolSize = 524288;
                           httpbinding.MaxReceivedMessageSize = 2147483647;
                           httpbinding.Security = new BasicHttpSecurity() { Mode = BasicHttpSecurityMode.None };
                           // 双向通信貌似有问题
                           if (context != null)
                           {
                               WSDualHttpBinding dualBingding = null;

                               dualBingding = new WSDualHttpBinding();
                               dualBingding.MaxReceivedMessageSize = 2147483647;
                               dualBingding.MaxBufferPoolSize = 524288;
                               dualBingding.MaxReceivedMessageSize = 2147483647;
                               dualBingding.ClientBaseAddress = new Uri("http://localhost:8667/ucm");
                               dualBingding.OpenTimeout = new TimeSpan(0, 0, 10);
                               dualBingding.Security = new WSDualHttpSecurity() { Mode = WSDualHttpSecurityMode.None };
                               myfactory = new DuplexChannelFactory<T>(context, dualBingding, new EndpointAddress(address));                             
                           }
                           else
                           myfactory = new ChannelFactory<T>(httpbinding, new EndpointAddress(address));
                           break;
                       default:
                           break;
                   }
                   return myfactory;
               }
               catch (Exception e)
               {
                   return null;
               }
           });

### 2、服务端开启
    static  ServiceHost service = new ServiceHost(typeof(ClientCallServer));
      service.AddServiceEndpoint(typeof(IClientCallServer), new BasicHttpBinding(), $"http://localhost:{ConfigurationFactory.Config.VideoPort}/ucm");
      service.open();
       }
