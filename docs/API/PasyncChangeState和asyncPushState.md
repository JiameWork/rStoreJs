## **asyncPushState**(name:string,fun:function():any,stroeName:string) 

## API说明：

asyncPushState和pushState方法使用无异，虽然使用方法相同，但其内部触发方式完全不同，您可以多次发从一个异步命名状态但是执行结果只会和首先发布的同步。

## **asyncChangeState**(changArr:[name:string,...value],stroeName:string)

## API说明：

asyncChangeState和changeState的使用方法也是相同的，但是值得注意的是asyncChangeState是一个异步函数，这意味着您要获取返回值的时候需要注意使用异步的方式获取返回值，异步总线是允许重复触发的。

!>如果是函数式组件请在useEffect中模拟的挂载完成的钩子中使用，如果是类式组件请在挂在完成钩子中使用，否则请勿在组件函数内使用asyncChangeState，因为asyncChangeState极有可能被react激发两次，后果就是导致await后或Promis.then中的代码看起来只能执行一次，但实际上会执行两次。

## 使用:
asyncChangeState通常和asyncPushState是一起使用的，但他们没有先后顺序，也就是说您可以触发一个未来才会发布的具名状态，您也可以使用asyncChangeState和asyncPushState进行常规总线触发。

```javascript
//父组件----start
import RComponents from './components/RStroeCompen'
import { creatProxy } from 'rstore-react'
import { useEffect } from 'react'

const App = ({ rStore }) => { //解构出props中的rStore
  const { testStore } = rStore.data //在rStore的总状态数据中解构出testStore的状态数据
  useEffect(() => { //组件装载完成后触发 注意子组件装载完成后父组件才有可能装载完成
        rStore.asyncPushState('testFun', (a, b, c) => { 
          console.log('happey', a, b, c); 
          return true 
        }, 'testStore')//'happey'，123, 456, 789
        //发布一个名叫testFun的异步总线回调参数为(a, b, c) => { console.log('happey', a, b, c)到testStore的异步总线中等待或直接激发，返回一个值并打印
    }, [])
  return (
    <div>
      <h1>{testStore.name}</h1>
      <h2>{testStore.old}</h2>
      <RComponents />
    </div>
  )
}
export default creatProxy(App, ['testStore'])//为App组件创建一个代理组件并接入testStore的rstore中

//---end

//子组件----start
import React, { useEffect } from 'react'
import { creatProxy } from 'rstore-react'
import { useEffect } from 'react'

const TestComponents = ({ rStore }) => {
     useEffect(async () => {
    console.log(await rStore.asyncChangeState(['testFun', 123, 456, 789], 'testStore'))//true
    //触发一个testStore中名称叫testFun并且很有可能未来才会发布的异步总线状态，并传入参数123, 456, 789，然后打印
  }, [])
    return (
        <div></div>
    )
}

export default creatProxy(TestComponents, ['testStore'])//为TestComponents组件创建一个代理组件并接入testStore的rstore中

//---end
```

