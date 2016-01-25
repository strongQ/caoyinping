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

{% highlight javascript %}
var myModule=angular.module("MyModule",[]);
myModule.controller('LoadDataCtrl',['$scope','$http',function($scope,$http){
    $http({
        method:'GET',
        url:'data.json'
    }).success(function(data,status,headers,config){
        console.log("success....");
        console.log(data);
        $scope.users=data;
    }).error(function(data,status,headers,config){
        console.log("error....");
    });
}])
{% endhighlight %}
要放在服务器上运行哦！尼玛读不出来啊...

### 3.指令用在不同控制器中

    <body>
      <div ng-controller="MyCtrl1">
        <loader howtoload="loadData1()">加载数据中</loader>
      </div>
      <div ng-controller="MyCtrl2">
       <loader howtoload="loadData2()">加载数据中</loader>
      </div>        
    </body>

js中
{% highlight javascript %}
var myModule=angular.module("MyModule",[]);
myModule.controller('MyCtrl1',['$scope',function($scope){
    $scope.loadData1=function(){
        console.log("加载数据中---1");
    }
}]);
myModule.controller('MyCtrl2',['$scope',function($scope){
    $scope.loadData2=function(){
        console.log("加载数据中---2");
    }
}]);
myModule.directive("loader",function(){
    return{
        restrict:"AE",
        link:function(scope,element,attrs){
            element.bind('mouseenter',function(event){
                // scope.loadData();
                // scope.$apply("loadData()");
                // 注意这里的坑，howToLoad会被转换成小写的howtoload
                scope.$apply(attrs.howtoload);
            });
        }
    }
});
{% endhighlight %}

### 4.独立Scope

html页：

    <div ng-controller="MyCtrl">
      Ctrl:
     <input type="text" ng-model="ctrlFlavor"></input>
      Directive:
      <drink flavor="ctrlFlavor"></drink>
    </div>

js中：
{% highlight javascript %}
var myModule=angular.module("MyModule",[]);
myModule.controller("MyCtrl1",['$scope',function($scope){
    $scope.ctrlFlavor="百威";
}])
myModule.directive("drink",function(){
    return{
        restrict:"AE",
        template:"<div>{{flavor}}</div>",
        scope:{
            flavor:'@'
        },
        //link:function(scope,element,attrs){
           // scope.flavor=attrs.flavor;        
    }
})
{% endhighlight %}
抱歉我自己看不懂啦@

### 5.动感超人指令


    <superman strength speed>动感超人---力量+敏捷  
	</superman>

js中：
{% highlight javascript %}
var myModule=angular.module("MyModule",[]);
myModule.directive("superman",function(){
    return{
        scope:{},
        restrict:'AE',
        controller:function($scope){
            $scope.abilities=[];
            this.addStrength=function(){
                $scope.abilities.push("strength");
            };
            this.addSpeed=function(){
                $scope.abilities.push("speed");
            };
            this.addLight=function(){
                $scope.abilities.push("ligth");
            };
        },
        link:function(scope,element,attrs){
            element.addClass('btn btn-primary');
            element.bind("mouseenter",function(){
                console.log(scope.abilities);
            });
        }
    }
});
myModule.directive("strength",function(){
    return{
        require:'^superman',
        link:function(scope,element,attrs,supermanCtrl){
            supermanCtrl.addStrength();
        }
    }
});
myModule.directive("speed",function(){
    return{
        require:'^superman',
        link:function(scope,element,attrs,supermanCtrl){
            supermanCtrl.addSpeed();
        }
    }
});
myModule.directive("light",function(){
    return{
        require:'^superman',
        link:function(scope,element,attrs,supermanCtrl){
            supermanCtrl.addLight();
        }
    }
});
{% endhighlight %}

### 6.表单验证

    <body>
     <form name="myForm" ng-submit="save()" 
	           ng- controller="TestFormModule">
         <input name="userName" type="text" 
		       ng-model="user.userName" required></input>
         <input name="password" type="password" 
		       ng-model="user.password" required></input>
         <input type="submit" ng-disabled="myForm.$invalid">
		 </input>         
</body>

### 7.表单数据双向绑定
html页：

    <body>
       <div class="panel panel-primary">
        <div class="panel-heading">
         <div class="panel-title">双向数据绑定</div>
         </div>
         <div class="panel-body">
         <div class="row">
         <div class="col-md-12">
          <form action="" role="form" class="form-horizontal"
		          ng-controller="UserInfoCtrl">
            <div class="form-group">
              <label class="col-md-2 control-label">
                邮箱：
              </label>
              <div class="col-md-10">
                <input type="email" class="form-control"
				   placeholder="推荐使用126邮箱" 
				      ng-model="userInfo.email">
              </div>
            </div>
            <div class="form-group">
              <label class="col-md-2 control-label">
                密码：
              </label>
              <div class="col-md-10">
                <input type="password" class="form-control"
                placeholder="只能是数字、字母、下划线" 
				     ng-model="userInfo.password">
              </div>
            </div>
            <div class="form-group">
              <div class="col-md-offset-2 col-md-10">
                <div class="checkbox">
                  <label >
                    <input type="checkbox" 
					 ng-model="userInfo.autoLogin" >自动登陆
                  </label>
                </div>
              </div>
            </div>
            <div class="form-group">
                     <div class="col-md-offset-2 col-md-10">
                        <button class="btn btn-default" 
						     ng-click="getFormData()">获取Form表单
							    上的值</button>           
                        <button class="btn btn-default" 
						     ng-click="setFormData()">设置Form表单
							   上的值</button>                  
                        <button class="btn btn-default" 
						    ng-click="restForm()">重置Form表单
							   上的值</button>
                     </div>
              </div>
            </form>
          </div>
        </div>
      </div>
     </div>
    </body>
js页：
{% highlight javascript %}
var userInfoModule = angular.module('UserInfoModule', []);
userInfoModule.controller('UserInfoCtrl', ['$scope', function ($scope) {
    $scope.userInfo = {
        email: "25244322@qq.com",
        password: "25244322",
        autoLogin: true
    };
    $scope.getFormData = function () {
        console.log($scope.userInfo);
    };
    $scope.setFormData=function(){
      $scope.userInfo={
        email:'damoqiongqiu@126.com',
        password:'damoqiongqiu',
        autoLogin:false
      }
    };
    $scope.restForm=function(){
      $scope.userInfo = {
        email: "25244322@qq.com",
        password: "25244322",
        autoLogin: true
    };
    }
}])
{% endhighlight %}
8.&绑定

    <greeting greet="sayHello(name)"></greeting>

js页：
{% highlight javascript %}
var myModule=angular.module("MyModule",[]);
myModule.controller('MyCtrl',['$scope',function($scope){
  $scope.sayHello=function(name) {
    alert("Hello "+name);
  }
}])
myModule.directive("greeting",function(){
  return{
    restrict:'AE',
    scope:{
      greet:'&'
    },
    template:'<input type="text" ng-model="userName"/><br/>'+
    '<button class="btn btn-default" ng-click="greet({name:userName})">Greet</button>'
  }
})
{% endhighlight %}
### 先转移到这吧！反正我——不会！
