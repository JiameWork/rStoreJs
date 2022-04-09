## **pushState**(name:string,fun:function():any,stroeName:string) 

## API说明：

pushState方法用于在rstore同步总线上发布一个具名的状态(方法)，通常是优先发布的

## **changeState**(changArr:[name:string,...value],stroeName:string)

## API说明：

changeState方法用来触发任何存在于rstore同步总线发布的状态，通常是后来发布的，注意此方法的返回值是回调函数执行的返回值

## 使用:
changeState通常和pushState是一起使用的，所以示例将两个API在一起使用会更容易阅读

```javascript
//父组件----start
import RComponents from './components/RStroeCompen'
import { creatProxy } from 'rstore-react'

const App = ({ rStore }) => { 
  const { testStore } = rStore.data //在rStore的data中取出testStore的data
  rStore.pushState('testFun', (a, b, c) => { console.log('happey', a, b, c) }, 'testStore')
  //发布一个名为testFun的同步状态到testStore同步总线中，状态的回调函数是(a, b, c) => { console.log('happey', a, b, c)
  return (
    <div>
      <RComponents />
    </div>
  )
}
export default creatProxy(App, ['testStore'])
//---end

//子组件----start
import React, { useEffect } from 'react'
import { creatProxy } from 'rstore-react'

const TestComponents = ({ rStore }) => {
    useEffect(() => { //组件挂载完成触发
        rStore.changeState(['testFun', 123, 456, 789], 'testStore') //仍然可以得到回调函数的返回值
        //触发一个同步状态在testStore同步总线中名叫testFun的同步状态，并为回到函数传入参数
    }, [])
    return (
        <div></div>
    )
}

export default creatProxy(TestComponents, ['testStore'])
//---end
```

