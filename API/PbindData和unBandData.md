## **bindData**(names:[string],rstoreName:string)

!> 注意，creatProxy生成的代理组件中如果要申请响应式的更新请在props.rStore.data中找到以对应rstore实例名称命名的状态数据进行更改 如props.rStore.data.testStore.name = '小J'，您也可以在storeHouse中找到原生的rstore实例进行更改 如props.storeHouse.testStore.data.name = '小J'，如果您觉的更改响应式数据太过繁琐请尝试[changeData](API/PchangeData.md)这个API

## API说明：

bindData是一个专门为类式组件提供响应式的API，为组件绑定后rstore会根据API所绑定的关键字和绑定的rstore实例来判断是否去更新组件。

## **unBindData**(names:[string],rstoreName:string)

## API说明：

unBindData是一个专门为类式组件解绑响应式的API，当bindData绑定组件有可能会被卸载时，请使用此api解绑，否则将可能引起无法预估的错误。


## 使用:
bindData和unBindData经常会面，同时说明更容易理解。
```javascript
//父组件----start

import RComponents from './components/RStroeCompen'
import { creatProxy } from 'rstore-react'
import { useEffect } from 'react'

testStore.data.name = '小王' //初始化testStore中的数据
testStore.data.old = 20

const App = ({ rStore }) => { //rStore将所有绑定store中的状态以rstore实例名的方式储存在rStore的data中
  rStore.bindData(['name', 'old'], 'testStore') //绑定testStore中名叫name和old的状态，更改testStore状态中的name或者old都会引起响应式更新。
  const { testStore: data } = rStore.data //在props.rStore.data中获取以testStore命名的状态数据库
  useEffect(() => {
    setTimeout(() => {
      rStore.unBindData(['old'], 'testStore')
    }, 3000) //3秒之后取绑testStore中名叫old的状态，更改testStore状态中的状态old之后rstore不会为您跟新此组件。
  }, [])
  return (
    <div>
      <h1>{data.name}</h1>
      <h2>{data.old}</h2>
      <button onClick={() => { data.old++ }}>年龄加+</button> //相当于rStore.data.testStore.old++
    </div>
  )
}
export default creatProxy(App, ['testStore'])

//---end
```