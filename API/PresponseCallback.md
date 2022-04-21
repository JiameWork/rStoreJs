## **responseCallback**(callBack:function,rstoreName:string)

## API 说明：

如果您不想让 rstore 接管更新您可以使用 responseCallback 来进行选择性更新，它的参数是一个回调函数并且每一次更新都会无差别调用回调函数并且回调参数会接收到全局状态数据，此 API 是目前代理 API 的最后一个 API，它的灵感来源于 redux！

!> 注意严厉禁止在 responseCallback 中无条件更改响应式数据，否组将会引起恐怖的无限回调。

## 新特性：

@2.0.3：现在 responseCallback 执行后会返回一个函数，函数执行即可取消继续订阅，状态更新再也不会调用取消订阅的回调函数。

## 使用:

```javascript
import RComponents from "./components/RStroeCompen";
import { creatProxy } from "rstore-react";

testStore.data.name = "小王"; //初始化testStore中的数据
testStore.data.old = 20;

const App = ({ rStore }) => {
  //rStore将所有绑定store中的状态以rstore实例名的方式储存在rStore的data中

  const { testStore: data } = rStore.data; //在props.rStore.data中获取以testStore命名的状态数据库
  useEffect(() => {
    rStore.responseCallback((data) => {
      //每次更新会无差别调用回调函数，您可以通过回调函数接收到的data数据进行选择性更新。
      //data是testStore中的状态数据库
      //data.old++ 请严厉禁止这种行为！
      if (data.old > 24) {
        console.log("该干点什么了");
      }
    }, "testStore");
  }, []);
  return (
    <div>
      <h1>{data.name}</h1>
      <h2>{data.old}</h2>
      <button
        onClick={() => {
          data.old++;
        }}
      >
        按钮
      </button>
    </div>
  );
};
export default creatProxy(App, ["testStore"]);
```
