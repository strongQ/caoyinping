### 1、依赖注入   
   Transient ：每次请求时都会创建，并且永远不会被共享。   
   Scoped ： 在同一个Scope内只初始化一个实例 ，可以理解为（ 每一个request级别只创建一个实例，同一个http request会在一个 scope内）      
   Singleton ：只会创建一个实例。该实例在需要它的所有组件之间共享。因此总是使用相同的实例。