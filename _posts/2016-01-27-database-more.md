---
layout:       post
title:           DataBase数据库操作
category:   数据库
tags:          [数据库,sqlite,mongodb,compact]

---

# 总结一些数据库连接

## 1.Mongodb启动
**mongod --dbpath d:\mongodb\data**

## 2.c#连接Access

{% highlight c# %}
<pre>
<add name="connstr" connectionString="Provider=Microsoft.Jet.OLEDB.4.0;Data Source=c:\users\cao\documents\visual studio 2013\Projects\Access\Access\db1.mdb" providerName="Access"/>
然后，OleDbConnection->OleDbCommand...
</pre>
{% endhighlight %}

## 3.c#连接sqlite

**dll必须引入的哦！**

  {% highlight c# %}
  <appSettings>
      <add key="dbPath" value="~/db/foodDB.db"/>
      <add key="sqlite" value="Data Source={0};Version=3"/>
  </appSettings>
然后：
       private static readonly string connStr = string.Empty;
        static SqliteHelper()
        {
            //1.读取数据库路径
            string dbPath = ConfigurationManager.AppSettings["dbPath"];
            string conn = ConfigurationManager.AppSettings["sqlite"];
            //2.读取连接字符串
            dbPath = HttpContext.Current.Request.MapPath(dbPath);
            //3.拼接
            conn = conn.Replace("{0}", dbPath);
            connStr = conn;
        }
{% endhighlight %}
