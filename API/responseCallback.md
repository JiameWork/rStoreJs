## **responseCallback**(callBack:function)

## API 说明：

如果您不想让 rstore 接管更新您可以使用 responseCallback 来进行选择性更新，它的参数是一个回调函数并且每一次数据更新都会无差别调用回调函数并且回调参数会接收到全局状态数据。

!> 注意严厉禁止在 responseCallback 中无条件更改响应式数据，否组将会引起恐怖的无限回调。

## 新特性：

@2.0.3：现在 responseCallback 执行后会返回一个函数，函数执行即可取消继续订阅，状态更新再也不会调用取消订阅的回调函数。

## 使用:

```javascript
import React, { Component } from "react";
import store from "../../testStore/store";
export default class index extends Component {
  render() {
    const { name, old } = store.data; //在this.rstore.data中解构出数据
    return (
      <div>
        <h1>名称：{name}</h1>
        绑定name
        <h2>年龄：{old}</h2>
        绑定old
        <button
          onClick={() => {
            store.data.old++;
          }}
        >
          选择性更新
        </button>
      </div>
    );
  }
  componentDidMount() {
    const unCallback = store.responseCallback((data) => {
      //每次更新都会调用回调并将data传入
      //data.old++ 请严厉禁止这种行为！
      if (data.old > 30) {
        //当data中的old大于30的时候进行更新
        this.setState({});
      }
    });
    unCallback(); //取消订阅，执行之后不会在调用回调函数
  }
}
```
