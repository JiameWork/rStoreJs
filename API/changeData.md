## **changeData**(data:object)

## API说明：

changeData为您提供了批量设置状态数据的方法，只能设置已有的数据无法新增。

!>注意无法新增，只能更改已有数据！

## 使用:
```javascript
import React, { Component } from 'react'
import store from '../../testStore/store'
const { data } = store 
data.name = '小K'   
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
                <button onClick={() => store.changeData({ name: '小U', old: 22 })}>更改名称和年龄</button>
            </div>
        )
    }
    componentDidMount() {
        store.bindData(['name', 'old'], this) //为此实例绑定data中的name和old数据的更新
    }
}

```