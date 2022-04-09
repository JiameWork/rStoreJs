## **bindData**(names:[string],instance:reactComponents)

## API说明：

bindData是一个专门为类式组件提供响应式的API，为组件绑定后rstore会根据API所绑定的关键字和组件实例来判断是否去更新组件。

## **unBindData**(names:[string],instance:reactComponents)

## API说明：

unBindData是一个专门为类式组件解绑响应式的API，当bindData绑定的组件实例有可能会被卸载时，请使用此api解绑，否则将可能引起无法预估的错误。


## 使用:
bindData和unBindData经常会面，同时说明更容易理解。

```javascript
import React, { Component } from 'react'
import store from '../../testStore/store'
const { data } = store //在store中解构出data
data.name = '小K'   //为data初始化数据
data.old = 18   //为data初始化数据
export default class index extends Component {
    render() { //如果想使用响应式数据还要解构数据，请一定在render中解构，如果不想就可以直接 data.name 同样可以
        const { name, old } = data//在data中解构出数据
        return (
            <div>
                <h1>名称：{name}</h1> 
                绑定name
                <h2>年龄：{old}</h2>
                绑定old
                <button onClick={() => {
                    data.name = '小O'
                    data.old = 20
                }}>更改名称和年龄</button>
                要通过data.xxx的形式更改数据否则将失去响应式，因为data是一个Proxy代理对象
            </div>
        )
    }
    componentDidMount() {
        store.bindData(['name', 'old'], this) //为此实例绑定data中的name和old数据的更新
    }
    componentWillUnmount() {
        store.unBindData(['name', 'old'], this) //为此实例解绑data中的name和old数据的更新
    }
}

```