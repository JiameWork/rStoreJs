## **mapData**(names:[string],instance:reactComponents)

## API说明：

mapData用来往组件实例身上映射rstore上的状态数据，它的独特的应用场景就是当您把rstore实例挂载到react的Component的原型上时，可以通过this.rStoreData方便的操作数据。

?>说明：将rstore挂载到Component的原型上可以不用引入rstore实例即可通过通过this.rstore的方式访问rstore，

## 使用:
```javascript
//App.jsx---start
import store from './testStore/store' //导入rstore实例
import { Component } from 'react'
Component.prototype.rstore = store //将rstore挂载到Component原型
const { data } = store //在store中解构出data
data.name = '小K'   //为data初始化数据
data.old = 18   //为data初始化数据
import ClassComponents from './components/ClassComponents'
export default () => {
  return (
    <div>
      <ClassComponents />
    </div>
  )
}
//---end


import React, { Component } from 'react'
export default class index extends Component {
    render() {
        const { name, old } = this.rstore.data//在this.rstore.data中解构出数据
        return (
            <div>
                <h1>名称：{name}</h1>
                绑定name
                <h2>年龄：{old}</h2>
                绑定old
            </div>
        )
    }
    componentDidMount() {
        this.rstore.bindData(['name', 'old'], this) //为此实例绑定data中的name和old数据的更新
        this.rstore.mapData(['name', 'old'], this) //将状态数据中的name、old映射到此组件的rStoreData身上
        const {rStoreData} = this //修改数据通过rStoreData.xxx的方式进行响应式更新，谨记切勿深度解构
        rStoreData.name = '小E'
    }
}


```