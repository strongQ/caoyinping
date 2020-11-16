---

layout:        post
title:         prism-summary tidy
description:   整理部分prism在wpf中的用法
keywords:      prism
categories:    [prism]
tags:          [prism]

---



### 1、 注册Region并使用

   (1). App继承PrismApplication,CreateShell方法中 return Container.Resolve<MainWindow>();     
   (2). 注册  发现region,regionManager.RegisterViewWithRegion("ContentRegion",typeof(ViewA));  加入 regionManager.Regions["ContentRegion"].Add(Resolve<ViewA>);     
   (3). view中使用 prism:RegionManager.RegionName="ContentRegion"       
   (4). view切换 Active与Deactive         
   (5). 导航方法，containerRegistry.RegisterForNavigation<ViewA>();<ViewB>,
   regionManager.RequestNavigate("ContentRegion","ViewA",NavigationComplete); 
   ViewModel继承INavigationAware,OnNavigatedTo方法中，navigationContext.Parameters["Person"];         
   返回：navigationContext.NavigationService.Journal.GoBack();
   参数：new NavigationParamters().Add("person",object); 
   备注：TabControl中的Region内容导航会自动生成 tabitem.   

### 2、 模块中注册Region
    
    App.config配置    
    (1). CreateModuleCatalg()方法重写，return new ConfigurationModuleCatalog();      
    (2). 配置模块信息,App.config中加入<modules>节点，<module assemblyFile="ModuleA.dll moduleType="" moduleName="ModuleAModule" startupLoaded="True">     

    Code配置  
    (1). ConfigureModuleCatalog(IModuleCatalog moduleCatalog)重写,moduleCatalog.AddModule<ModuleA.ModuleAModule>();   
    按需导入配置 AddModule(new ModuleInfo(){
        ModuleName=xx.Name,
        ModuleType=xx.AssemblyQualifiedName,
        InitializationModel=InitializationModel.OnDemand
    })

    Directory配置
    (1). CreateModuleCatalog()方法重写，return new DirectoryModuleCatalog() {ModulePath=@".\Modules"}

   (3). 初始化模块项目，ModuleAModule:IModule,OnInitialized方法中注册Region.     
   (4). 自动注入ViewModel,prism:ViewModelLocator.AutoWireViewModel="True,默认view后加ViewModel.手动配置，重写ConfigureViewModelLocator,ViewModelLocationProvider.Register<MainWindow,CustomViewModel>();    




   
