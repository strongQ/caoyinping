---
layout: post
title: Ajax基础
category: ajax
tag: ajax
---

来总结一下ajax操作。

## 基础版本
{% highlight javascript %}
 //点击按钮实现注册功能
            document.getElementById('btnRegister').onclick = function () {
                var loginId, pwd1, pwd2;
                loginId = document.getElementById('txtLoginId').value;
                pwd1 = document.getElementById('txtPassword1').value;
                pwd2 = document.getElementById('txtPassword2').value;

                //------向服务器发起Post请求---------------------
                var xhr = XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject("Microsoft.XMLHttp");
                xhr.onreadystatechange = function () {
                    //如果服务器返回1，表示注册成，返回0表示注册失败,2表示两次密码不一致！
                    if (xhr.readyState == 4 && xhr.status == 200) {
                        if (xhr.responseText == '1') {
                            alert('注册成功！');
                            window.location.href = 'Login.htm';
                        } else if (xhr.responseText == '2') {
                            alert('两次密码不一致！');
                        } else {
                            alert('注册失败！');
                        }
                    }
                };
                xhr.open('post', 'ProcessRegister.ashx', true);
                xhr.setRequestHeader('content-type', 'application/x-www-form-urlencoded');
                xhr.send('loginId=' + loginId + '&pwd1=' + pwd1 + '&pwd2=' + pwd2);
            };
{% endhighlight %}
---------------------------------------------------
## Jquery版本
{% highlight javascript %}
var configObj = {
       method   //数据的提交方式：get和post
       url   //数据的提交路劲
       async   //是否支持异步刷新，默认是true
       data    //需要提交的数据
       dataType   //服务器返回数据的类型，例如xml,String,Json等
       success    //请求成功后的回调函数
       error   //请求失败后的回调函数
    } 
$.ajax(configObj);//通过$.ajax函数进行调用
{% endhighlight %}
*$.getJSON，这个是进一步的封装，也就是对返回数据类型为Json进行操作。里边就三个参数，需要我们设置，非常简单：url,[data],[callback]*
