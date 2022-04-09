# **为您介绍RStore状态总线管理工具**
!> 提示:由于类式组件和函数式的差异性，API的标签上将会有五种标记**类式、函数式、通用、实验性、代理**，API内容按特性分为**总线API**、**状态API**和**代理API**，状态API的内容只足够让类式组件进行响应式更新，您如果想了解函数式组件如何使用rstore中进行响应式更新请移步[creatProxy](API/creatProxy.md)章节

标记说明

    类式：仅有类式组件可以使用

    函数式：仅有函数式可以使用，（目前暂时没有函数式专属API）

    通用：所有场景通用

    实验性：目前在实验阶段的API，稳定性会有所降低

    代理：仅有代理组件可以使用
    
## **介绍**
?>  RStore原本是仅仅用于React事件总线的一款工具但RStore绝不应该仅限于事件总线，所以一段时间后RStore成为了一款专门为react打造的状态管理 + 库 + 总线工具，您可以使用状态管理来规范的管理库中的状态，如果追求方便，您也可以直接操作库中的状态，RStore还有两套不同特性的事件总线会在特性中介绍。

## **特性**

1.**同步事件总线**

2.**异步事件总线**

3.**状态管理工具**

4.**全局状态**

5.**选择性状态更新**

6.**全组件支持**



## **安装**

```powershell
npm i rstore-react
```



## **简单的RStore**

```javascript
import { creatRStore } from 'rstore-react' //引入rrstore

const store = { //rstore初始化对象

    offBus: true, //关闭事件总线只用于状态管理

    name: 'testStore', //rstore名称

    data: { //初始化data数据
        name: '小王',
        old: '124',
        nums: 0
    },

    actions(store) { //定义actions方法
        const testFun = () => {
            store.data.name = '小张'
            store.data.name = '12'
        }
        return {
            testFun
        }
    }
}

export default creatRStore(store) //创建一个rstore并且暴露
```

## **联系我或报告BUG**
?>如果您需要联系开发者或者反应BUG请在下方评论区评论，您将会获得最快的回复。


