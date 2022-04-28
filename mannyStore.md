## **多 rstore 并行**

## 说明：

通过了解 rstore 众多的 API，您应该可以发现 rstore 为多 rstore 实例并行预留了很多接口，从 creatRStore 初始化实例推荐填写的 rstore 名称或 creatProxy 中的第二参数为数组格式的仓库名称绑定，您都可以发现 rstore 运行并行的身影，您可以在下方的启发演示中熟悉多 rstore 实例并行的使用和它的优点。

!>注意此部分具有一定的复杂性，推进您熟悉基础 API 后阅读。

## 创建多个仓库

reqStore.js 仅仅当作仓库使用，关闭总线功能

```javascript
import { creatRStore } from "rstore-react";
import request from "request";
const store = {
  name: "firstStore",
  offBus: true,
  data: {
    goodList: [],
    userData: {},
    titel: "",
    miniTitel: "",
  },
  actions(rstoreData) {
    const reqUserData = async (userID, userPassword) => {
      if (!userID || !userPassword) return;
      const { code, data } = await request.get(
        `https://gitee.com/little-jia-jia/userdata?id=${userID}&password=${userPassword}`
      );
      if (code === 200) {
        rstoreData.userData = data;
      } else {
        console.log(new Error("get user Err!"));
      }
    };
    const reqBannerList = async () => {
      const { code, data } = await request.get(
        `https://gitee.com/little-jia-jia/bannerList`
      );
      if (code === 200) {
        rstoreData.bannerList = data;
      } else {
        console.log(new Error("get bannerList Err!"));
      }
    };
    const reqPushAddress = async (data) => {
      if (!data) return;
      const { code, data } = await request.post({
        method: "post",
        url: "https://gitee.com/little-jia-jia/address",
        data,
      });
      if (code === 200) {
        return "success";
      } else {
        return new Promise(new Error("err push address"));
      }
    };
    const setSumTitel = (titel, miniTitel) => {
      if (titel && titel.length > 5 && miniTitel && miniTitel.length > 10) {
        rstoreData.changeData({ titel, miniTitel });
      }
    };
    return {
      reqUserData,
      reqBannerList,
      reqPushAddress,
      setSumTitel,
    };
  },
};

export default creatRStore(store);
```

busStore.js 如果仅仅把 rstore 当作总线使用那么 rstore 实例会非常的轻便

```javascript
import { creatRStore } from "rstore-react";
const store = {
  name: "busStore",
  offStore: true,
  actions(rstore) {
    //可以在此调用总线功能
    rstore.pushState("test", () => {
      //发布一个同步总线
      console.log("will nice");
    });
    rstore.changeState("test", 123, 345, 567); //不建议在此直接触发总线，因为很有可能时无意义的,请尝试下方写法
    const changUserID = (id) => {
      //试试这样做
      const { changeState } = rstore;
      rstore.changeState("setId", `id=${id}rt_ew135_dtert_dsfcxv`);
    };
    rstore.asyncPushState("asyncTest", () => {
      //发布一个异步步总线
      console.log("async will nice");
    });
    rstore.asyncChangeState("asyncFun", 123, 456, 789); //触发一个有可能未来才会发布并且名称为asyncFun的异步总线
    return { changUserID };
  },
};

export default creatRStore(store);
```

## 使用多个仓库的注意项

main.js

```javascript
import React from "react";
import ReactDOM from "react-dom";
import "./mannyStore/busstore";
import "./mannyStore/firststore"; //如果需要使用creatProxy，那么请引入APP组件之前创建商店，这样可能很好的避免一些找不到商店的问题
import App from "./App";

ReactDOM.render(<App />, document.getElementById("root"));
```

### 代理组件使用多个仓库

```javascript
import { creatProxy } from "rstore-react";
import React, { Component } from "react";

class App extends Component {
  render() {
    const { firstStore: data } = this.props.rStore.data; //使用firstStore中的响应式数据
    return (
      <div>
        <h1>{data.titel}</h1>
        <h2>{data.miniTitel}</h2>
      </div>
    );
  }
  componentDidMount() {
    const { changeState, actions, bindData } = this.props.rStore; //解构方法
    bindData(["titel", "miniTitel"], "firstStore"); //绑定数据
    const { firstStore } = actions;
    changeState(["test"], "busStore"); //触发在busStore初始化对象actions中发布的命名同步状态
    firstStore.setSumTitel(
      "1234567891235",
      "这里是副标题，它的长度肯定要超过16个字符了"
    ); //触发actions中的预设方法
  }
}

export default creatProxy(App, ["firstStore", "busStore"]);
```

### 普通组件使用多个仓库

```javascript
import React, { Component } from "react";
import busstore from "./mannyStore/busstore";
import firststore from "./mannyStore/firststore";

export default class App extends Component {
  render() {
    const {
      data: { titel, miniTitel },
    } = firststore; //使用firstStore中的响应式数据
    return (
      <div>
        <h1>{titel}</h1>
        <h2>{miniTitel}</h2>
      </div>
    );
  }
  componentDidMount() {
    firststore.bindData(["titel", "miniTitel"], this); //绑定动态数据，仅是适用class组件，如果函数式组件需要响应式请使用creatProxy来进行相应式更新
    busstore.changeState("test"); //触发在busStore初始化对象actions中发布的命名同步状态
    firststore.actions.setSumTitel(
      "1234567891235",
      "这里是副标题，它的长度肯定要超过16个字符了"
    ); //触发actions中的预设方法
  }
}
```
