---
layout:                  post
title:                       简单的多线程应用
category:              多线程
tags:                    [多线程,随机数]

---

多线程运用的太少，先记录个最简单的，直到熟练!

{% highlight c# %}
1.随机数

 delegate void setdelegatenum(Label one,int a);
        private bool begin = false;
        private Thread td = null;
        private void setnum(Label one ,int a)
        {
           one.Text =a.ToString();
        }
        private void getnum( object label)
        {
            Label one = (Label)label;
            setdelegatenum a = this.setnum;
            Random random=new Random(unchecked((int)DateTime.Now.Ticks));
            while (true)
            {
                int num = random.Next(0, 9);
                this.Invoke(a, one, num);
                Thread.Sleep(500);
            }         
        }
        private void button1_Click(object sender, EventArgs e)
        {
            if (begin == false)
            {               
                Thread th = new Thread(new ParameterizedThreadStart(this.getnum));
                
                th.IsBackground = true;
                th.Start(label1);td = th;
                begin = true;
                button1.Text = "停止";
            }
            else
            {
                td.Abort();
                begin = false;
                button1.Text = "开始";
            }
        }

{% endhighlight %}