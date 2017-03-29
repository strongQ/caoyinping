---
layout:    post
title:     opc服务器
category:  项目
tags:      [wpf]
---

### 设置界面
![](https://img.alicdn.com/imgextra/i1/232721121/TB2TneVlC0mpuFjSZPiXXbssVXa_!!232721121.png)
### 数据导入界面
![](https://img.alicdn.com/imgextra/i4/232721121/TB2rCBvjNXkpuFjy0FiXXbUfFXa_!!232721121.png)

### 用到的第三方库
1、wpf皮肤  `MahApps.Metro`   
2、Excel操作  `NPOI.Extension`    
3、数据库操作  `Dapper`        
4、opc  `ASOPCDA.dll`

### 程序结构
#### 1、核心库 OPCBasic   
包含 OController<T>--每一个服务对应一个OController

包含 OPCManager单例--包含opc服务通用流程

T 必须继承ICommand、IWakeTag      

      /// <summary>
      /// 命令接口
      /// </summary>
     public interface ICommand
     {
      /// <summary>
      /// 获取任务取消
      /// </summary>
      /// <returns></returns>
      CancellationTokenSource GetCanellationTokenSource();
      /// <summary>
      /// 授权
      /// </summary>
      /// <returns></returns>
      bool ActiveCode();
       /// <summary>
       /// 注册
       /// </summary>
       bool RegServer();
       /// <summary>
       /// 注销
       /// </summary>
       void UnregServer();
       /// <summary>
       /// 初始化系统运行参数和分配服务器资源
       /// </summary>
       bool CreateServer();
       /// <summary>
       /// 释放服务器资源
       /// </summary>
       void FreeServer();
      /// <summary>
      /// 加载回调
      /// </summary>
       void AddCallBack();
   }

数据模拟方式必须继承IWakeValue接口

        interface IWakeValue
        {
        /// <summary>
        /// 刷新时间
        /// </summary>
         int RefreshTime
        {
            get;
            set;
        }      
        /// <summary>
        /// 模拟名称
        /// </summary>
        /// <returns></returns>
        string GetWayName();
        /// <summary>
        /// 模拟ID
        /// </summary>
        /// <returns></returns>
        int  GetWayID();

        /// <summary>
        /// 刷新时间
        /// </summary>
        /// <returns></returns>
        int GetRefreshTime();
        /// <summary>
        /// 全部刷新
        /// </summary>
        /// <param name="tagList"></param>
        /// <param name="second"></param>
        void RefreshAll(List<Tag> tagList,CancellationTokenSource cts);
        /// <summary>
        /// 验证数据
        /// </summary>
        /// <param name="tagList"></param>
        void ValidateDatas(List<Model.Tag> tagList);
        /// <summary>
        /// 单个刷新
        /// </summary>
        /// <param name="tag"></param>
        /// <param name="second"></param>
        void RefreshSingle(Tag tag);
    }


  其中随机模拟方式的RefreshAll实现如下：

    public void RefreshAll(List<Model.Tag> tagList,CancellationTokenSource cts)
       {
           CancellationToken ct = cts.Token;
           Task.Factory.StartNew(() =>
           {
               ValidateDatas(tagList);

               while (true)
               {
                   if (ct.IsCancellationRequested) break;

                   foreach (var tag in tagList)
                   {
                       tag.Value = (double)(rnd.Next((int)(tag.TheDataSIM.InitialValue * 100), (int)(tag.TheDataSIM.InitialValue + tag.TheDataSIM.ModeValue) * 100) + 1) / 100;
                       tag.Quality = 192;
                       tag.TimeStamp = DateTime.Now.ToLocalTime().ToFileTime();
                       DLLHelper.UpdateTagWithTimeStamp(tag.Handle, tag.Value, tag.Quality, tag.TimeStamp);
                   }
                   Thread.Sleep(RefreshTime);
               }
           }, ct);
       }

#### 实现库LocalOPC
定义了OPC信息的来源，实现了OPCBasic库中的接口。

#### 配置界面EditPro
配置加载点位信息和数据模拟信息
wpf特有的mvvm数据绑定，真的很好用

    public class ServerInfoViewModel
    {
        public DelegateCommand SaveCmd { get; set; }
        public DelegateCommand LoadCmd { get; set; }
        public ServerInfo serverModel { get; set; }
        public ServerInfoViewModel()
        {   
            this.serverModel = new ServerInfo();
            this.LoadCmd = new DelegateCommand();
            this.SaveCmd = new DelegateCommand();
            this.LoadCmd.ExecuteCommand = new Action<object>(this.serverModel.LoadData);
            this.SaveCmd.ExecuteCommand = new Action<object>(this.serverModel.SaveData);
        }
    }
