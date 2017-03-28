---
layout:   post
title:    端口映射器
category: 项目
tags:     [winform,网络]
---

### 一、端口映射器的作用
[![](https://img.alicdn.com/imgextra/i4/232721121/TB2_GL2lhBmpuFjSZFsXXcXpFXa_!!232721121.png)](https://github.com/strongQ/portMapper)

该软件在以下场合适用：   
（1）、需要隐藏真正TCP服务器的位置。    
（2）、限制客户端IP登陆    
（3）、TCP转地址

### 二、实现原理
1、根据输入ip和端口创建socket服务监听客户端。   
2、当新客户端连进来后，创建新的socket对象，连接到输出ip和端口创建的服务器上。     
3、交换两个socket对象的数据。


### 三、部分方法

        /// 清理Socket
        private void RunTaskClearSocket()
        {
            var task = Task.Factory.StartNew(() =>
            {
                while (true)
                {
                    for (int i = 0; i < PmList.Count; i++)
                    {
                        if (PmList[i].socketList.Count > 0 && IsClear)
                        {
                            List<Socket> tempSocketList = PmList[i].socketList;

                            for (int j = 0; j < tempSocketList.Count; j++)
                            {
                                Socket ts = tempSocketList[j];
                                try
                                {
                                  // 判断socket是否仍在通信
                                    if (ts.Poll(10, SelectMode.SelectRead))
                                    {

                                        int nRead = ts.Receive(new byte[] { 0 });
                                        if (nRead == 0)
                                            new ListHelpEvge(PmList[i], ts, false);
                                    }

                                }
                                catch
                                {
                                    new ListHelpEvge(PmList[i], ts, false);
                                    try
                                    {
                                        ts.Shutdown(SocketShutdown.Both);
                                        ts.Close();
                                    }
                                    catch (Exception) { }

                                }
                            }
                        }
                    }
                    Thread.Sleep(1000);
                }

            }).ContinueWith(x =>
            {
                try
                {
                    x.Wait();
                }
                catch { }
            });
        }
