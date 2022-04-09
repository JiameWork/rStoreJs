## **creatProxy**(component:function|class,stores:[string])

## API说明：

!>注意：如果您想要使用creatProxy，请注意仓库的创建顺序，不然您在仓库创建之前调用，您仍然将得到一个代理组件但是此代理组件不会接入任何的仓库，那么代理组件将失去意义，推荐您在在src/main.js中这样编写。

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import './testStore/store'
import App from './App' //组件引入之前创建rstore您可以仅引入而不使用它，但是这样就可以在所有的组件创建之前就为creatProxy准备好需要对接的rstore实例。

ReactDOM.render(
  <App />,
  document.getElementById('root')
)

```

creatProxy是一个非常重要的API，所以单独拿出一个分类来介绍creatProxy的使用，在creatProxy出现之前，rstore不能很好的与函数式组件兼容，creatProxy能够为任何类型的组件创建一层代理，并将更新的权力移交给代理组件，这意味着api组件可以脱离类式组件this的约束，为组件通props的方式传入纯API，因此它让rstore兼容所有的组件，因为props中的纯API重写了大部分方法，所以这一分类是来说明重写API的用法和特性。 


!>creatProxy可以完美兼容react-router-dom，和hooks函数，但是他有可能存在一定的兼容性问题有待证实，因此目前为creatProxy定型为实验性类型！

## 使用:
```javascript
import React from 'react'
import { creatProxy } from 'rstore-react'

const TestComponents = (props) => { 
    //props至少会收到两个参数rStore、storeHouse和其他您通过Proxy代理组件传入的props参数，其中rStore是专门为代理组件封装的方法，storeHouse是您绑定的原生rstore实例
    //访问某个rstore的data rStore.data.
    return (
        <div></div>
    )
}
export default creatProxy(TestComponents, ['testStore']) 
//第一个参数可以是函数式组件或者类式组件，第二个参数是要绑定的store的name
//用creatProxy创建一个代理组件然后将组件返回
//向代理组件传入props时，代理组件会原封不动的传给元组件，但是请注意不要和rstore要使用的属性名重复，否则会被rstore的属性覆盖。
```