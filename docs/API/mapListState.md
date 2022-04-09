## **mapListState**(names:[string],instance:reactComponents)

## API说明：

mapListState是一个专门为类式组件设置的API,它可以批量的把rstore实例中的的同步命名状态转为以函数的形式映射在类式组件实例身上。

!>注意mapListState只能映射同步的命名状态！

## 使用:

完整的使用
```javascript
//父组件---start
import store from './testStore/store' //导入rstore实例
import ClassComponents from './components/ClassComponents'
export default () => {
  store.pushState('testFun1', () => {
    console.log('victory')
  })
  store.pushState('testFun2', () => {
    console.log('handsome')
  })
  store.pushState('testFun3', () => {
    console.log('veryGood')
  })
  store.pushState('testFun4', () => {
    console.log('nething')
  })
  return (
    <div>
      <ClassComponents />
    </div>
  )
}
//---end

//子组件---start
import React, { Component } from 'react'
import store from '../../testStore/store'

export default class index extends Component {
    render() {
        return (
            <div>index</div>
        )
    }
    componentDidMount() {
        store.mapListState(['testFun1', 'testFun2', 'testFun3','testFun4'], this)
        this.testFun1()//"victory"
        this.testFun2()//"handsome"
        this.testFun3()//"veryGood"
        this.testFun4()//'nething'
    }
}

//---end

```