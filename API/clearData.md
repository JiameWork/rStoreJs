## **clearData**(names:[string])

## API说明：

clearData可以批量的清理仓库中的状态，然后让绑定关键词的实例进行最后一次更新。

!>注意再访问值为undefined，清理数据之前请避免还在使用即将清理数据的多级结构数据！


## 使用:
```javascript
import React, { Component } from 'react'
import store from '../../testStore/store'
const { data } = store 
data.name = '小K' //初始化数据  
data.old = 18   
export default class index extends Component {
    render() { 
        const { name, old } = data
        return (
            <div>
                <h1>名称：{name}</h1> 
                绑定name
                <h2>年龄：{old}</h2>
                绑定old
                <button onClick={() => store.clearData(['name','old'])}>清理名称和年龄</button>
            </div>
        )
    }
    componentDidMount() {
        store.bindData(['name', 'old'], this) //为此实例绑定data中的name和old数据的更新
    }
}

```