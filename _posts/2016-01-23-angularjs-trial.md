---

layout:    post
title:     Angularjs上手
category:  Angularjs
tags:      [前端,Angularjs]

---

## 当初调研了一点AngularJS，稍微记录记录

1.日期格式过滤

<pre>
    <body>
    {{ 1304375948024 | date }}
    <br>
    {{ 1304375948024 | date:"MM/dd/yyyy @ h:mma" }}
    <br>
    {{ 1304375948024 | date:"yyyy-MM-dd hh:mm:ss" }}
    <br>        
    </body>
</pre>

### 结果是:
