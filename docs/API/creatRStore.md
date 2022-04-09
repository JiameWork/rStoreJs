## **creatRStore**(options?:object)

## API说明：

creatRStore是rstore使用中第一个接触到的api,他的作用只有一个就是初始化一个新的rstore然后返回创建好的rstore实例。

## 使用:

最基本的使用
```javascript
import { creatRStore } from 'rstore-react' //引入rrstore

export default creatRStore() //创建rstore并导出
```
完整的使用
```javascript
import { creatRStore } from 'rstore-react'
/* 
{
	offBus?:boolen,
	offStore?:boolen,
	name?:string,
	data?:object,
	actions?:function(store):object
}
*/

const store = {
    //用于特异化rstore功能
    offBus: true, //此项为真则关闭总线
    offStore: true, //此项为真则关闭状态和状态管理 
    //用于rstore命名，这非常关键，如果忽略此项可能会妨碍您在函数组件中正常使用rstore，并且尽量避免重复
    name: 'testStore',
    //初始化状态仓库，必须是对象类型！
    data: {
        name: '小王',
        old: '124',
        nums: {
            num: {
                num: 0
            }
        }
    },
    //定义用来管理store实例
    actions(store) { //可以接收到创建的rstore，rstore上的仓库和多半方法都可以在此调用
        const testFuntiond = () => {
            console.log(store)
        }
        return { //返回值object类型，返回值需要时函数，不然无意义
            testFuntiond
        }
    }
}
// 注意初始化对象所有的属性都是可选的，rstore不依赖配置对象也可以正常工作，但是进行初始化配置会让rstore更好的工作
export default creatRStore(store)//将可选的初始化对象传入，rstore会根据传入的初始化对象进行初始化，

```