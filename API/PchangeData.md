## **changeData**(data:object,storeName:string)

## API说明：

changeData为您提供了批量设置状态数据的方法，只能设置已有的数据无法新增。

!>注意无法新增，只能更改已有数据！

## 使用:

```javascript
import RComponents from './components/RStroeCompen'
import { creatProxy } from 'rstore-react'

testStore.data.name = '小王' //初始化testStore中的数据
testStore.data.old = 20

const App = ({ rStore }) => { //rStore将所有绑定store中的状态以rstore实例名的方式储存在rStore的data中
  rStore.bindData(['name', 'old'], 'testStore') //绑定testStore中名叫name和old的状态，更改testStore状态中的name或者old都会引起响应式更新。
  const { testStore: data } = rStore.data //在props.rStore.data中获取以testStore命名的状态数据库
  return (
    <div>
      <h1>{data.name}</h1>
      <h2>{data.old}</h2>
      <button onClick={() => {
        rStore.changeData({ //批量更改已有的状态数据
          name: '小黑',
          old: "???"
        }, 'testStore')
      }}>换人</button>
    </div>
  )
}
export default creatProxy(App, ['testStore'])
```