---
layout:           post
title:            Winform运用
description:      一些工作中收集的代码
keywords:         winform
categories :      [winform]
tags :            [winform, 工作]
---

winform 一些常用方法

------------------------
## 1.窗体拖动
              /* 窗体拖动事件 */
              private const int WM_NCHITTEST = 0x84;
              private const int HTCLIENT = 0x1;
              private const int HTCAPTION = 0x2;
              protected override void WndProc(ref Message m)
             {
                switch (m.Msg)
              {
                case WM_NCHITTEST:
                    base.WndProc(ref m);
                    if ((int)m.Result == HTCLIENT)
                        m.Result = (IntPtr)HTCAPTION;
                    return;
                case 0xA3:
                    return;
               }
                  base.WndProc(ref m);
        }
        
## 2.限制多程序运行
 using (new Mutex(true, Assembly.GetExecutingAssembly().FullName, out newMutexCreated))
                {
                    if (!newMutexCreated)
                    {
                        MessageBox.Show("服务程序已经启动");
                        System.Environment.Exit(0);
                    }
                }
                
   static bool CheckRunning()
        {
            bool result = false;
            int ProceedingCount = 0;
            System.Diagnostics.Process[] Processes;
            Processes = System.Diagnostics.Process.GetProcessesByName("Server"); foreach (System.Diagnostics.Process IsProcedding in Processes)
            {
                if (IsProcedding.ProcessName == "Server")
                {
                    ProceedingCount += 1;
                }
            }
            if (ProceedingCount > 1)
            {
                MessageBox.Show("服务程序已经启动", "提示", MessageBoxButtons.OK, MessageBoxIcon.Error);
                result = true;
            }
            return result;
        }
               



