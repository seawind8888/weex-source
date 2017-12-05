`render/native/index.js`

```JavaScript
export default function init (config) {
  runtimeConfig = config || {}  
  frameworks = runtimeConfig.frameworks || {}
  initTaskHandler()

  // Init each framework by `init` method and `config` which contains three
  // virtual-DOM Class: `Document`, `Element` & `Comment`, and a JS bridge method:
  // `sendTasks(...args)`.
  for (const name in frameworks) {  
    //根据不同框架初始化Vue、Rax等框架
    const framework = frameworks[name]
    framework.init(config)
  }
  // 使frameworks继承Native交互方法

  // 继承注册Native组件方法，此方法用于给客户端发送通知渲染客户端组件
  adaptMethod('registerComponents', registerComponents) 

  // 用于调用客户端moudle
  adaptMethod('registerModules', registerModules)
  
  // 给组件传入方法
  adaptMethod('registerMethods')
  // 
  ; ['destroyInstance', 'refreshInstance', 'getRoot'].forEach(genInstance)

  return methods
}


```