## **rStore-TypeScript**

## 版本说明：

经过一段时间的兼容性改造，rStore-TypeScript 版本已经逐渐完善，rStore-TypeScript 版本是 rStore-JavaScript 版本的兼容和改造版，它的大部分 API 和 JavaScirpt 版本相同，但又有所删减和增加，因为依附 TypeScript，rStore 拥有了很好的 API 提示和类型提示，但类型提示并不适用于全部 API，在下方会详细讨论 TypeScript 版本的改动。

!>rStore-TypeScript 目前属于前瞻版本，rStore-Javescript 的写法也会逐渐向 rStore-TypeScrip 靠拢，但 rStore-TypeScript 尚不稳定，仍然可能存在潜在 BUG

## 安装 rstore-ts：

```powershell
npm i rstore-react-ts
```

## 删除的 API：

1 ~~mapListState~~ **类式** :mapListState 是一个专门为类式组件设置的 API,它可以批量的把 rstore 实例中的的同步命名状态转为以函数的形式映射在类式组件实例身上，但在 TypeScript 中，因为强类型限制，mapListState 不能很便捷的再将方法映射的类式组件上，所以 TypeScript 版本中确定删除。

2 ~~mapData~~ **类式** :mapData 用来往组件实例身上映射 rstore 上的状态数据，它的独特的应用场景就是当您把 rstore 实例挂载到 react 的 Component 的原型上时，可以通过 this.rStoreData 方便的操作数据,但同样是因为强类型限制，在 TypeScript 版本中确定删除。

3 ~~getStore~~ **通用** :getStore 是一个文档里未提及的 API 但是它真实存在于源码当中，因为它实用性低使用场景少。所以并未在文档中说明，并且它很有可能在未来的更新迭代中删除，getStore 的作用是在任何时间和位置通过异步的方式获取 store 仓库中已创建的实例，但它的作用微乎其微，TypeScript 版本中确定删除。

## 新增的 API

1 新增两个核心 API，creatRBus 和 creatRStatus，他们代替了 rStore 实例的可选功能，可选功能会造成实例的不稳定，所以 creatRBus 和 creatRStatus，分别分担两个独立的功能，详情见下方说明

```javascript
import { creatRBus, creatRStatus } from "./Rstore/main";

/*creatRBus配置对象格式
    {
        name:string,
        actions?:(bus)=>{[name:string]:Function} //方法收到的参数是一个仅仅有总线功能的rstore，参数没有data也不能操作除总线之外的功能
    }
*/

const bus = creatRBus({
  //在creatRBus中，它的配置对象格式为
  name: "bus", //必填
  actions(bus) {
    //选填
    bus.pushState("test", () => console.log("happy"));
    return {};
  },
});

/* creatRStatus配置对象格式
    {
        name:string,
        data:object,
        actions:(bus)=>{[name:string]:Function} //方法收到的参数是一个仅仅有总线功能的rstore，参数没有data也不能操作除总线之外的功能
    }
*/

const status =
  creatRStatus <
  { test: string, text: string } >
  {
    //creatRStatus需要传一个泛型类型作为初始化data的类型
    data: {
      //在creatRStatus中数据初始化时必须的
      test: "hello", //必须和定义的类型相兼容 必填
      text: "成功了",
    },
    name: "testStatus", //初始化名称 必填
    actions(status) {
      //会接收到一个仅有全局状态功能的rstore，并且参数有很好的类型提示
      //status.data.test = 1 //不能将类型“number”分配给类型“string”。
      const testFun = () => console.log(status.data.test);
      return { testFun };
    },
  };
```

## 更改的 API 和数据格式:

1 changeState 和 asyncChangeState 参数类型由 (name:string,...value) 改为 (name:string,value?:any[]),第二个参数改为可选数组，也就是说，多个参数必须以数组的形式传入比如 changeState('name',[123,456,789])对应状态回调函数就会收到 123,456,789 为参数

2 creatRStatus 的初始化参数有所改动，不允许选择 creatRStatus 构建的实例功能，在 TypeScript 中 creatRStatus 实例包含所有的功能，并且 creatRStatus 需要接收一个泛型类型作为 data 的类型，以下为演示

```javascript
/* 
    {
        data: object
        name: string
        actions: (store) => { [name: string]: Function }
    }
*/

export default creatRStore <
  { name: string, old: number } >
  {
    data: {
      //data必须进行初始化，并且需要通过泛型传入数据类型
      name: "小王", //类型必须和泛型相兼容
      old: 12,
    },
    actions(store) {
      //接收到store为参数
      return {}; //返回的必须是对象并且属性全部是函数类型
    },
    name: "storeName", //name属于必填项并且要注意不要同名，虽然不会报错，但使用中很有可能影响您的编程效率
  };
```

3 creatProxy 所创建的组件会接收到 props，并且 props 接收到的值已经从{~~rStore,storehouse~~}改为 {rStore,rBus,rStatus},在 TypeScript 中的由于组件接收到的参数 props 参数不能有很好的类型提示，所以请导入 creatProxy 的同时导入 rstore 准备好的类型 ProxyTypes，因为 creatRBus, creatRStatus 的出现，rstore 为他们在代理组件的 props 中进行了分区，通过 creatRStore 创建的实例会筛选在 rStore 中，通过 creatRStatu 创建的实例会筛选在 rStatus 中，通过 creatRBus 创建的实例会筛选在 rBus 中，在下方会详细介绍他们的新用法。

```javascript
import React, { useEffect } from "react";
import { creatProxy, ProxyTypes } from "./Rstore/main"; //导入creatProxy API和 ProxyTypes 类型
/* rStore的格式
    {
        //用法没变
        data: {
            [store名称]: store名称对应的状态数据
        }
        actions: {
            [store名称]: store名称对应的actions
        }
        bindData: (names:Array<string>,storeName:string) => void
        unBindData: (names:Array<string>,storeName:string) => void
        responseCallback: (fun: Function, storeName: string) => void
        clearData: (dataKeys: Array<string>, storeName: string) => void
        pushState: (str: string, fun: Function, storeName: string) => void
        changeData: (datas:Arrat<string>,storeName:string) => void
        clearMethods: (mothodsNameArr: Array<string>, isAsync: boolean, storeName: string) => void
        asyncPushState: (str: string, fun: Function, storeName: string) => void
        //用法改变

        changeState: (data: { [name: string]: any }, storeName: string) => void

        changeState的参数由([string,...value],storeName:string) 变为 (data: { [name: string]: any }, storeName: string)这意味着实例名是第一参数，参数数组是第二参数，而非全集成一个数组

        asyncChangeState: 和如上变化一样
    }
*/
/* rStatus rStore的格式去掉总线部分API 用法相同
    {
         data: {
            [store名称]: store名称对应的状态数据
        }
        actions: {
            [store名称]: store名称对应的actions
        }
        bindData: (names:Array<string>,storeName:string) => void
        unBindData: (names:Array<string>,storeName:string) => void
        responseCallback: (fun: Function, storeName: string) => void
        clearData: (dataKeys: Array<string>, storeName: string) => void
        pushState: (str: string, fun: Function, storeName: string) => void
        changeData: (datas:Arrat<string>,storeName:string) => void
    }
*/

/* rBus rStore的格式去掉全局状态部分API 用法相同
    {
         actions?: {
            [store名称]: store名称对应的actions
        }
        clearMethods: (mothodsNameArr: Array<string>, isAsync: boolean, storeName: string) => void
        asyncPushState: (str: string, fun: Function, storeName: string) => void
        changeState: (data: { [name: string]: any }, storeName: string) => void
        asyncChangeState: (data: { [name: string]: any }, storeName: string) => void

    }
*/

const App: React.FC<ProxyTypes> = function (props) {
  //利用React.FC规定组件的props类型
  useEffect(() => {
    props.rStore.bindData(["name", "old"], "storeName"); //绑定rStore实例中实例名称为storeName的全局状态name和old
    props.rStatus.bindData(["test", "text"], "testStatus"); //绑定rStatus实例中实例名称为storeName的全局状态test和text（rStatus实例是由creatRStatus创建的）
    props.rBus.changeState("hello", [123, 4345], "bus"); //触发rBus实例中的实例名称为bus的同步总线中的hello并传入参数123，4345
  }, []);
  props.rBus.pushState(
    "hello",
    (a: number, b: number) => console.log("胜利", a, b),
    "bus"
  ); //发布一个名为hello的状态名称到实例名为bus的rBus实力中的同步总线中等待触发回调
  const { testStatus } = props.rStatus.data; //取出rStatus实例中的名为testStatus的数据
  const { storeName } = props.rStore.data; //取出rStore实例中的名为storeName的数据
  return (
    <div>
      <h1>{storeName.name}</h1>使用rStore实例名为storeName的状态数据
      <h1>{storeName.old}</h1>
      <h1>{testStatus.test}</h1>使用rStatus实例名为testStatus的状态数据
      <h1>{testStatus.text}</h1>
      <button
        onClick={() => {
          props.rStore.changeData(
            {
              //批量设置rStore中的名为storeName的rStore实例的状态数据
              name: "小黑",
              old: 1,
            },
            "storeName"
          );
          props.rStatus.changeData(
            {
              //批量设置rStatus中的名为testStatus的rStatus实例的状态数据
              test: "吃饭了吗您",
              text: "好吃吧您",
            },
            "testStatus"
          );
        }}
      >
        换人
      </button>
    </div>
  );
};

export default creatProxy(App, ["storeName", "testStatus", "bus"]); //数组中的实例名称可以是creatRStore、creatRBus、creatRStatus创建出来实例的名称，rstore会根据数组中的实例名称把对应实例筛入对应的分区
```
