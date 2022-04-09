## **asyncPushState**(name:string,fun:function():any) 

## API说明：

asyncPushState和pushState方法使用无异，虽然使用方法相同，但其内部触发方式完全不同，您可以多次发从一个异步命名状态但是执行结果只会和首先发布的同步。

## **asyncChangeState**(name:string,...value)

## API说明：

asyncChangeState和changeState的使用方法也是相同的，但是值得注意的是asyncChangeState是一个异步函数，这意味着您要获取返回值的时候需要注意使用异步的方式获取返回值，异步总线是允许重复触发的。

!>如果是函数式组件请在useEffect中模拟的挂载完成的钩子中使用，如果是类式组件请在挂在完成钩子中使用，否则请勿在组件函数内使用asyncChangeState，因为asyncChangeState极有可能被react激发两次，后果就是导致await后或Promis.then中的代码看起来只能执行一次，但实际上会执行两次。

## 使用:
asyncChangeState通常和asyncPushState是一起使用的，但他们没有先后顺序，也就是说您可以触发一个未来才会发布的具名状态，您也可以使用asyncChangeState和asyncPushState进行常规总线触发。

```javascript
//父组件----start
import store from './testStore/store'
import RStroeCompen from './components/RStroeCompen'
import { useEffect } from "react" //导入useEffect模拟声明周期函数

export default () => {
  useEffect(async() => { //异步的方式命名状态的返回值 否则很有可能获得的是 Promis 不要直接写在根函数体内
      const result = await store.asyncChangeState('testFun', 123, 456, 789) 
      console.log(result) //"成功了"
  }, [])
  return (
    <div>
      <RStroeCompen />
    </div>
  )
}

//---end

//子组件----start
import React from 'react'
import store from '../../testStore/store'

export default function index() {//发布一个过去触发的命名状态并返回一个值
    store.asyncPushState('testFun', (a, b, c) => { console.log(a, b, c); return '成功了' }) //"123, 456, 789"
    return (
        <div>index</div>
    )
}

//---end
```

