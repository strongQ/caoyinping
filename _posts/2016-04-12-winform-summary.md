---

layout:           post
title:            Winform运用
description:      一些工作中收集的代码
keywords:         winform
categories :      [winform]
tags :            [winform, 工作]

---

### 1.窗体拖动

<% highlight c# %>
              #region 窗体拖动事件
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
              #endregion 窗体拖动事件
<% endhighlight %>




