## **clearMethods**(names:[string],isAsync:boolean,stroeName:string)

## API说明：

clearMethods用于在销毁当前有已有的命名状态，它可用于组件销毁时，或者更改命名对应的状态时。
clearMethods存在第二个参数如果为真则清理异步总线反之清理同步总线，现在它在代理组件当中可以通过一个方法清理不同仓库


!>清理多余的命名状态是很有必要的，为了避免未知错误，请您规划好命名状态的发布、触发、销毁，这非常有必要。

## 使用:
```javascript
//父组件----start

import RComponents from './components/RStroeCompen'
import { creatProxy } from 'rstore-react'
import { useEffect } from 'react'

const App = ({ rStore }) => { 
  useEffect(async () => {
    rStore.asyncChangeState(['asyncTestFun', 123, 456, 789], 'testStore')//触发testStore异步总线中名为asyncTestFun的异步状态
  }, [])
  rStore.pushState('testFun', () => { return 1 + 1 }, 'testStore') //向testStore的同步总线中发布testFun的状态并追加回调函数
  return (
    <div>
      <RComponents />
    </div>
  )
}
export default creatProxy(App, ['testStore'])

//---end

//子组件----start

import RComponents from './components/RStroeCompen'
import { creatProxy } from 'rstore-react'
import { useEffect } from 'react'

const TestComponents = ({ rStore }) => {
    useEffect(() => {
        rStore.changeState(['testFun', 'well good'], 'testStore')
        rStore.asyncPushState('asyncTestFun', (a, b, c) => { console.log('happey', a, b, c); return true }, 'testStore')
        setTimeout(() => {
            rStore.clearMethods(['testFun'], false, 'testStore') //清理同步方法 清理testStore同步总线中的testFun命名状态
            rStore.clearMethods(['asyncTestFun'], true, 'testStore') //清理异步方法 清理testStore异步总线中的asyncTestFun命名状态
            rStore.changeState(['testFun', 'well good'], 'testStore') //激发打印错误 Method has not been loaded 
            rStore.asyncChangeState(['testFun', 123, 456, 789], 'testStore') //激发无效 等待下一次异步触发
        }, 6000)
    }, [])
    return (
        <div></div>
    )
}

export default creatProxy(TestComponents, ['testStore'])

//---end
```