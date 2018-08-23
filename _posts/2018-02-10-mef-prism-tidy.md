---

layout:        post
title:         mef-prism tidy
description:   整理部分mef在prism中的小用法
keywords:      mef-prism
categories:    [mef,prism]
tags:          [mef,prism]

---

----------------------------

### 1、mef在cs中的应用
    DirectoryCatalog directoryCatalog = new DirectoryCatalog("imps");
    var container = new CompositionContainer(directoryCatalog);
    container.ComposeParts(this);// this为 new Program();

### 2、mef在bs中的应用
    var catalog = new DirectoryCatalog(System.IO.Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "Imps"));
    var container =new CompositionContainer(catalog);

    var resolver = new MEFDependencyResolver(container);
    //Store the container to the configuration context
    container.ComposeExportedValue<CompositionContainer>(container);

    //Install MEF dependency resolver for Web API
    config.DependencyResolver = resolver;//config为HttpConfiguration


### 3、mef在prism中的应用

##### 3.1、在MefBootstrpper中创建默认的行为
     //创建默认的Region行为
      protected override IRegionBehaviorFactory ConfigureDefaultRegionBehaviors()
        {
            var factory = base.ConfigureDefaultRegionBehaviors();

            factory.AddIfMissing("AutoPopulateExportedViewsBehavior", typeof(AutoPopulateExportedViewsBehavior));

            return factory;
        }
##### 3.2、行为继承RegionBehavior,IPartImportsSatisfiedNotification
    [Import]
     private IRegionManager Manager { get; set; }

    [ImportMany(AllowRecomposition = true)]
     public Lazy<object, IViewRegionRegistration>[] RegisteredViews { get; set;

     // override OnAttach,Implementation OnImportsSatisfied
       private void AddRegisteredViews()
          {

              if (this.Region != null)
              {
                  foreach (var viewEntry in this.RegisteredViews)
                  {
                      if (viewEntry.Metadata.RegionName == this.Region.Name)
                      {
                          var view = viewEntry.Value;

                          if (!this.Region.Views.Contains(view))
                          {
                              this.Region.Add(view);
                          }
                      }
                  }
              }
          }

##### 3.3、构建特性便于操作
    [MetadataAttribute]
    public class ViewExportAttribute : ExportAttribute, IViewRegionRegistration
     {
    public ViewExportAttribute()
        : base(typeof(object))
    { }

    public ViewExportAttribute(string viewName)
        : base(viewName, typeof(object))
    { }

    public string RegionName { get; set; }

    public string ViewName { get { return base.ContractName; } }
    }


##### 3.4、使用方法
    [ViewExport(RegionName = InstructRegionNames.INSTRUCT_MAIN_PREVIEW_REGION)]
    注册reginon,方便使用

##### 3.5、设置config配置和读取
     <module assemblyFile="Plugin/Service/JP.HCZZP.WPFApp.Service.dll" moduleType="JP.HCZZP.WPFApp.Service.ServiceModule, JP.HCZZP.WPFApp.Service" moduleName="ServiceModule" startupLoaded="true"/>
     代码中加载：
       IModuleManager moduleManager = Container.GetExportedValue<IModuleManager>();
          ModulesConfigurationSection modules = ConfigurationManager.GetSection("modules") as ModulesConfigurationSection;
          foreach (ModuleConfigurationElement module in modules.Modules)
              moduleManager.LoadModule(module.ModuleName);
