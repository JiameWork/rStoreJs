## **pushState**(name:string,fun:function():any) 

## API说明：

pushState方法用于在rstore同步总线上发布一个具名的状态(方法)，通常是优先发布的

## **changeState**(name:string,...value)

## API说明：

changeState方法用来触发任何存在于rstore同步总线发布的状态，通常是后来发布的，注意此方法的返回值是回调函数执行的返回值

## 使用:
changeState通常和pushState是一起使用的，所以示例将两个API在一起使用会更容易阅读

```javascript
//父组件----start
import store from './testStore/store' //导入rstore实例
import RStroeCompen from './components/RStroeCompen'//导入子组件
export default () => {
  store.pushState('testFun', (a,b,c) => {  
    //pushState 第一参数为一个string类型的唯一状态名 第二参数为一个可以接受多个参数的回调函数
    console.log('victory',a,b,c)
  })
  return (
    <div>
      <RStroeCompen />
    </div>
  )
}
//---end

//子组件----start
import React from 'react'
import store from '../../testStore/store' //导入rstore实例

export default function RStroeCompen() {
    store.changeState('testFun',123,456,789)
    //changeState 第一参数为一个string类型的唯一状态名，其他参数为状态触发后回调函数的形参，返回值是状态触发的回调函数后的返回值
    return (
        <div>index</div>
    )
}
//---end
```

