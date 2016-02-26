---
layout:                   post
title:                      国际化简单的文件读取（json)
category:              json
tags:                     [json,字符串,国际化]

---

公司系统需要英文版本，因此这种简单机械的活就交给我这个小白了。

## 1.

因为拿的是excel英文对应文档，在组json的过程中我就想投机取巧，找了半天终于找到一个 *xlsx2json-master*      
程序，一键全部转换为json格式数据

## 2.
select 下拉必须组成 data-i18n("xx")格式，特别是民族等竟然不允许我从数据库中获取，嫌麻烦就自己写了个小程序组:
**第一个是c#解析json用的，我只用到第二个单纯分割字符**
{% highlight c# %}
1.读取函数
首先要安装库 Install-Package Newtonsoft.Json
  private string getjson()
        {
            StringBuilder str = new StringBuilder();
            str.Append("");
            string path = System.Environment.CurrentDirectory + "\\json.txt";
            FileStream fs = new FileStream(path, FileMode.Open);
            StreamReader m_streamReader = new StreamReader(fs);
            str.Append(m_streamReader.ReadToEnd());
            m_streamReader.Close();
            m_streamReader.Dispose();
            fs.Close();
            fs.Dispose();
            return str.ToString();
        } 
2.机械运动
    string str = getjson();
            StringBuilder b = new StringBuilder();
           // List obj=JsonConvert.DeserializeObject<List>(str);
            string[] a = str.Split(',');
            for (int i = 0; i < a.Length; i++)
            {
                string[] name = a[i].Split('"');
                //b.AppendLine(string.Format("{1}",name[1],name[3] ));
               b.AppendLine(string.Format("i18n.t(\"Hospital.{0}\")",name[1]));
            }
            textBox1.Text = b.ToString();     

3.文本形式
"name":"yy",
"yy":"xx"           
{% endhighlight %}

## 正式开始国际化

1.引入js
{% highlight javascript %}
<script src="../assets/js/i18next.js" type="text/javascript"></script>
{% endhighlight %}
2.初始化

{% highlight javascript %}
  function initLang() {
            $.i18n.init({
                detectLngQS: 'lang',
                useCookie: false,
                fallbackLng: 'en',
                resGetPath: "Locales/__ns__.__lng__.json",
                ns: {
                    namespaces: ['translation'],
                    defaultNs: 'translation'
                }
            }, function (t) {
                $("body").i18n();
            });
        }
  {% endhighlight %}
  
3.应用

data-i18n="xx" //html页中

i18n.t("xx") //js中
当然得是json数据哦！

### 4.遇到的问题

1.jqgrid label无法应用
解决办法：function(t)中 jqgrid方法

