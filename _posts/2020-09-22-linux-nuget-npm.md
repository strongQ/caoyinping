---

layout:        post
title:         linux操作整理
description:   nuget和npm私有服务
keywords:      [nuget,npm,linux]
categories:    [linux]
tags:          [linux,nuget]

---

----------------------------


### 1、启动后台运行

     nobup npm run serve

### 2、linux加入key
    
     eval "$(ssh-agent -s)"

     ssh-add ~/ .ssh/id_rsa

### 3、Baget部署nuget包
    
     1、创建一个名为baget.env的文件，用来存储BaGet的配置信息：

     sudo vim baget.env
     在该文件中录入以下信息：（其中，ApiKey为推送nuget包时需要使用的，得记得。具体配置信息可参考baget官方文档）


     ApiKey=1234567890
 
     Storage__Type=FileSystem
     Storage__Path=/var/baget/packages
     Database__Type=Sqlite
     Database__ConnectionString=Data Source=/var/baget/baget.db
     Search__Type=Database
    2、运行BaGet：

     在baget.env同级目录下，创建baget-data文件夹，用来持久化bagat状态


    sudo mkdir baget-data
    拉取baget镜像


     docker pull loicsharma/baget
     运行


     docker run --restart=always --name nuget-server -p 5000:80 --env-file baget.env -v "$(pwd)/baget-data:/var/baget" -d loicsharma/baget:latest



### 4、部署npm私服

     docker run -it -d --name verdaccio -p 4873:4873 verdaccio/verdaccio


### 5、向nuget私服上传包

   
      Get-ChildItem -Path ./_packages | ForEach-Object -Process{
       dotnet nuget push $_.fullname --skip-duplicate  -k 4F557FE6-E922-444E-8F0B-50851E6A9A07 -s http://beibei.press:5000/v3/index.json
       }

### 6、Xamarin打包Apk

      1. docker pull nathansamson/xamarin-android-docker
      2. docker run -it -v $(pwd):/xamarin_project chiticariu/xamarin-android /bin/bash
      3. msbuild OwnApp.Android.csproj /p:AndroidSdkDirectory=/android/sdk /restore /t:SignAndroidPackage /p:Configuration=Release


### 7、部署nginx

      1. docker run -d -p 2222:80 --name nginx  -v "$(pwd)/www:/usr/share/nginx/html" -v "$(pwd)/conf/nginx.conf:/etc/nginx/nginx.conf" -v "$(pwd)/logs:/var/log/nginx" nginx:latest
      2. docker cp 13afb35cbc98:/etc/nginx/nginx.conf ./nginx/conf
