## **仓库的初始化**

?>在rstore中，初始化仓库不是硬性要求，但是初始化仓库的确可以为您提供一种快速搭建仓库结构的捷径，如果您还没不知道怎么初始化，请移步[creatRStore](/API/creatRStore.md)中查看初始化仓库的方式，如果您想通过其他方式初始化仓库，请在使用数据之前通过rstore实例.data.xxxx = any 的形式搭建数据，不可直接去更改data的内存指向。


## **规范管理rstore实例中的状态数据**

通过初始化对象属性的方法actions，您可以规范的去操作store中的数据，让仓库的数据不那么容易出现意外，您返回的方法可以在rstore实例.actions中使用他们按照预期的规划管理仓库。

```javascript
import { creatRStore } from 'rstore-react'
const store = {
    //..........
    actions(store) { //可以接收到创建的rstore，rstore上的仓库和多半方法都可以在此调用
        const testFuntiond = () => {
            console.log(store)
        }
        return { //返回值object类型，返回值需要时函数，不然无意义
            testFuntiond
        }
    }
}
export default creatRStore(store)//创建rstore实例
```
## **仓库的响应式**

通过正确更改状态仓库的值，让其绑定数据响应式的组件动态去更新.

```javascript
const { data } = store
data.name = 'K' //正确

const {name} = data.name
name = 'J' //错误
```