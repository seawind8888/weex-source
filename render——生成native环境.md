`render/native/index.js`

```JavaScript
import setup from './setup'
import frameworks from '../../frameworks/index'

setup(frameworks)  //环境生成入口，开始装载js-framework环境

```

`render/native/setup.js`

```JavaScript

import { subversion } from '../../../package.json'
import runtime from '../../runtime'
import services from '../../services/index'

/**
 * Setup frameworks with runtime.
 * You can package more frameworks by
 *  passing them as arguments.
 */
export default function (frameworks) {
  const { init, config } = runtime
  config.frameworks = frameworks
  const { native, transformer } = subversion

  //注册services模块，主要包含BroadcastChannel通信模块
  for (const serviceName in services) { 
    runtime.service.register(serviceName, services[serviceName])
  }

  // 使用Object.freeze冻结Document,Comment等类，防止修改或增加属性值
  runtime.freezePrototype()
  // 设置debug-log等级
  runtime.setNativeConsole()

  // register framework meta info
  global.frameworkVersion = native
  global.transformerVersion = transformer

  // init frameworks
  const globalMethods = init(config)   
  // 传入Document、Element、TaskCenter、sendTasks等类方法，生成Native DOM APIs


  // set global methods
  for (const methodName in globalMethods) {  
    // 注册与Native交互的全局事件，createInstance、registerComponents等
    global[methodName] = (...args) => { 
      const ret = globalMethods[methodName](...args)
      if (ret instanceof Error) {
        console.error(ret.toString())
      }
      return ret
    }
  }
}


```