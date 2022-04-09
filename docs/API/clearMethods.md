## **clearMethods**(names[string],isAsync:boolean)

## API说明：

clearMethods用于在销毁当前有已有的命名状态，它可用于组件销毁时，或者更改命名对应的状态时。
clearMethods存在第二个参数如果为真则清理异步总线反之清理同步总线


!>清理多余的命名状态是很有必要的，为了避免未知错误，请您规划好命名状态的发布、触发、销毁，这非常有必要。

## 使用:
```javascript
//父组件----start

import store from './testStore/store' //导入rstore实例
import RStroeCompen from './components/RStroeCompen'
export default () => {
  store.pushState('testFun', (a, b, c) => {
    console.log('victory', a, b, c)
  })
  store.asyncPushState('asyncTestFun', (a, b, c) => {
    console.log('victory', a, b, c)
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
    store.changeState('testFun', 123, 456, 789)
    setTimeout(() => {
        store.clearMethods(['testFun']) //清理名称叫testFun的同步状态
        store.clearMethods(['asyncTestFun',true]) //清理名称叫testFun的同步状态
        store.changeState('testFun', 123, 456, 789) // react_devtools_backend.js:2450 Method has not been loaded --you are string as testFun (此错误不会影响运行只作为提醒使用)
    }, 3000)
    return (
        <div>...</div>
    )
}

//---end
```