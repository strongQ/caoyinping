### 1、依赖注入   
     Transient ：每次请求时都会创建，并且永远不会被共享。   
     Scoped ： 在同一个Scope内只初始化一个实例 ，可以理解为（ 每一个request级别只创建一个实例，同一个http request会在一个 scope内）      
     Singleton ：只会创建一个实例。该实例在需要它的所有组件之间共享。因此总是使用相同的实例。

### 2、特性标记，程序集注入
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method | AttributeTargets.Property)]
    public class SingletonAttribute : Attribute
    {
        /// <summary>
        /// 是否使用自身的类型进行注入
        /// </summary>
        public bool Itself { get; set; }

        /// <summary>
        /// 是否使用自身的类型进行注入
        /// </summary>
        /// <param name="itself"></param>
        public SingletonAttribute(bool itself = false)
        {
            Itself = itself;
        }
    }

     var singletonAttr = (SingletonAttribute)Attribute.GetCustomAttribute(type, typeof(SingletonAttribute));

### 3、根据配置加载缓存
      var assembly = AssemblyHelper.LoadByNameEndString($".Lib.Cache.{config.Provider.ToString()}");
      Check.NotNull(assembly, $"缓存实现程序集({config.Provider.ToString()})未找到");
      var handlerType = assembly.GetTypes().FirstOrDefault(m => typeof(ICacheHandler).IsAssignableFrom(m));
      if (handlerType != null)
       {
          services.AddSingleton(typeof(ICacheHandler), handlerType);
       }