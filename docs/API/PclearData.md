## **clearData**(names:[string],rstoreName:string)

## API说明：

clearData可以批量的清理仓库中的状态，然后让绑定关键词的实例进行最后一次更新。

!>注意再访问值为undefined，清理数据之前请避免还在使用即将清理数据的多级结构数据！

## 使用:

```javascript
import RComponents from './components/RStroeCompen'
import { creatProxy } from 'rstore-react'

testStore.data.name = '小王' //初始化testStore中的数据
testStore.data.old = 20

const App = ({ rStore }) => { //rStore将所有绑定store中的状态以rstore实例名的方式储存在rStore的data中
  rStore.bindData(['name', 'old'], 'testStore') //绑定testStore中名叫name和old的状态，更改testStore状态中的name或者old都会引起响应式更新。
  const { testStore: data } = rStore.data //在props.rStore.data中获取以testStore命名的状态数据库
  setTimeout(() => {
    rStore.clearData(['name', 'old'], 'testStore')//5秒后销毁名为testStore的rstore实例中的名叫name和old的状态，并进行最后一次响应更新。
  }, 5000)
  return (
    <div>
      <h1>{data.name}</h1>
      <h2>{data.old}</h2>
    </div>
  )
}
export default creatProxy(App, ['testStore'])
```