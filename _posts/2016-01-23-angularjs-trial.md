---

layout:    post
title:     Angularjs上手
category:  Angularjs
tags:      [前端,Angularjs]

---

## 当初调研了一点AngularJS，稍微记录记录

1.日期格式过滤


    <body>
    {{ 1304375948024 | date }}
    <br>
    {{ 1304375948024 | date:"MM/dd/yyyy @ h:mma" }}
    <br>
    {{ 1304375948024 | date:"yyyy-MM-dd hh:mm:ss" }}
    <br>        
    </body>


### 结果是:

    May 3, 2011
    05/03/2011 @ 6:39AM
    2011-05-03 06:39:08
 再来看，前面的数字弄啥类！

### 2.加载json

html页：

    <body>
     <div ng-controller="LoadDataCtrl">
      <ul>
       <li ng-repeat="user in users">
            {{user.name}}
       </li>
      </ul>
     </div>
    </body>

js中：

