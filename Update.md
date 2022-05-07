### **测试版本 2.0.1** 2022-4-6

1. 此版本是正式公开测试的版本，是的 rstore 目前仍然处于测试阶段，rstore 还要在不断的测试中不停的敲打锤炼才能稳定发布！
2. 第一版的文档上线
3. 官方网站上线
4. 修复若干 BUG

### **测试版本 2.0.3** 2022-4-10

1. rstore-typescript 版本上线了，rstore-ts 的版本号将会和 rstore-js 同步，后期 rstore-js 会逐渐向 rstore-ts 的特性靠拢
2. 为 responseCallback 增加了一个回调函数，回调函数的执行会导致订阅失效，失效后数据更新不会再调用回调函数，方法是 rstore-js 和 rstore-ts 通用的

### **测试版本 2.0.4** 2022-4-21

1. 修复了深层次无法进行响应式更新的问题
2. 优化了 rstore-ts 的部分源码

### **rstore-ts 测试版本 2.0.5** 2022-5-1

1. 优化了数据结构、响应式算法、体积大小。
2. js 版本将于不久之后和 ts 版本同步全部 API 消除差异化。

### **rstore-ts 测试版本 2.0.6** 2022-5-7

1. 增加 API useData,有了它再也不用手动绑定响应式，即用即绑
2. 响应式修改，所有的响应式全部交给一个“异步 CPU”触发，带来了更好的性能提升，但是也意味着 rstore 的响应式成为了一个异步的微任务，在下一个版本会考虑为 rstore 加入一个类似 Vue 的 nextTick 的回调函数来更轻松些的解决异步带来的影响。
3. 增加 API createRStore，现在再重拾 rstore-js 的一些方便的设计！createRStore 可以使 RStore-ts 创建仓库，状态，或者总线。
4. 修复对深层次数组或对象解绑响应式会失败的 BUG，封装和删除多余冗杂代码。
5. 由于近期 rStore 可能会频繁的改善和更新功能，文档会在相对稳定时更新，这里会简单描述新增 API 的使用方法。

注意事项:虽然现在是异步更新，responseCallback 依然会在仓库数据改变时就调用并传入最新值，因为 responseCallback API 的设计初衷并不是依赖于异步的，依赖于异步会让 responseCallback 实用性消弱并且不那么容易理解和使用。

## useData

```javascript
import { useEffect } from "react";
import { creatProxy, ProxyTypes } from "rstore-react-ts";
const Test = function Test(props: ProxyTypes) {
  const { useData } = props.rStatus;
  /*
     // useData(storeName:string):{
         data:{[name:string]:any} ,
         unBind:(keys?:string[])=>void 
         }
  */
  const testData = useData("global"); //在Proxy中调用需要传入商店名 这里就是使用的global的status仓库
  const { data } = testData; //data是可以被解构的，unBind同样可以，但是要注意命名冲突
  useEffect(() => {
    setTimeout(() => {
      console.log("事件触发");
      testData.unBind(["testArr"]);
      //解绑数组中指定的属性，如果不传入参数，那么data就会完全丧失响应式，但是如果您尝试修改，其他绑定组件依然会得到响应式更新。
    }, 3000);
  }, []);
  return (
    <div>
      <button
        onClick={() => {
          data.testData.name = "小河"; //修改同样会带来响应式 这样修改虽然很方便，但是“太过方便”，推荐您使用actions规范使用。（I'll let you in on a little secret 类式组件或单独使用rstore实例都可以获得更好的强类型提示，但是目前的代理API还不行）
          data.testArr.push("123123123");
        }}
      >
        进行深层次的响应式触发
      </button>
      <button
        onClick={() => {
          data.testArr.push("123123123");
        }}
      >
        进行深层此数组方法
      </button>
      <h1>{data.testData.name}</h1>{" "}
      直接使用即可获得响应式不需要再bindData，当然如果您需要bindData时，它也是完全可用的。
      <h2>{data.testArr}</h2>
    </div>
  );
};

export default creatProxy(Test, ["global"]);
```

## createRStore

```javascript
import { createRStore } from "../rstore-react-ts";
import { ReactElement } from "react";
import request from "../api";
const { reqRight } = request;
type dynamicMenuFunValueType = Array<{
  title: string;
  key: string;
  icons?: ReactElement;
  pagepermisson?: 1;
  children?: dynamicMenuFunValueType;
}>;
interface data {
  titel: string;
  userName: string;
  slectList: dynamicMenuFunValueType;
  roules: Array<string>;
  userRegion: string;
  testData: {
    name: string;
  };
  testArr: Array<string>;
}
export default createRStore<data>({
  type: "status", //在此声明rstore类型即可，并且不同类型可以获得不同类型的强类型提示，十分推进您使用此API，此后此API也会被作为主力使用，因为它符合rStore的设计初衷。
  data: {
    titel: "首页",
    userName: localStorage.getItem("userName") || "",
    slectList: [],
    roules: [],
    userRegion: localStorage.getItem("userRegion") || "",
    testData: {
      name: "小王",
    },
    testArr: ["123123123"],
  },
  name: "global",
  actions(status) {
    const changeTitel = (value: string) => {
      if (value) {
        status.data.titel = value;
      }
    };
    const changeUserName = (value: string) => {
      if (value) {
        localStorage.setItem("userName", value);
        status.data.userName = value;
      }
    };
    const changSlectList = async () => {
      const result = (await reqRight()) as unknown;
      (result as dynamicMenuFunValueType).forEach((item) => {
        if (item.children && item.children.length === 0) {
          delete item.children;
        }
      });
      status.data.slectList = result as dynamicMenuFunValueType;
    };
    return { changeTitel, changeUserName, changSlectList };
  },
});


```

PS：一个人同时维护代码和文档多少有些力不从心，请原谅文档中可能出现的小错误和小纰漏。
