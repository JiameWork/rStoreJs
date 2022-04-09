## **actions规范管理**

## 说明：
正确的使用actions可以使您更有效的管理仓库，定义了actions后您就可以通过actions预定的方法操作rstore的总线或者全局状态，而非亲自去操作，这样可以大大避免一些无意识的失误，这里仅仅做一个简单的启示。

## 演示actions的正确使用

store.js
```javascript
import { creatRStore } from 'rstore-react'
import request from 'request'
const store = {
    name: 'testStore',
    data: {
        goodList: [],
        userData: {},
    },
    actions({data:rstoreData}) { //接收到rstore实例并从中结构出data然后重命名rstoreData
        const reqUserData = async (userID, userPassword) => {
            if (!userID || !userPassword) return
            const { code, data } = await request.get(`https://gitee.com/little-jia-jia/userdata?id=${userID}&password=${userPassword}`)
            if (code === 200) {
                rstoreData.userData = data
            } else {
                console.log(new Error('get user Err!'))
            }
        }
        const reqBannerList = async () => {
            const { code, data } = await request.get(`https://gitee.com/little-jia-jia/bannerList`)
            if (code === 200) {
                rstoreData.bannerList = data
            } else {
                console.log(new Error('get bannerList Err!'))
            }
        }
        const reqPushAddress = async (data) => {
            if (!data) return
            const { code, data } = await request.post({
                method: 'post',
                url: 'https://gitee.com/little-jia-jia/address',
                data
            })
            if (code === 200) {
                return 'success'
            } else {
                return new Promise(new Error('err push address'))
            }
        }
        return {
            reqUserData, reqBannerList, reqPushAddress
        }
    }
}


export default creatRStore(store)
```

### 代理组件使用actions
```javascript
import { creatProxy } from './Rstore/main'

const App = ({ rStore }) => { 
  const userData = {
    id: '',
    password: ''
  }
  const { testStore: action } = rStore.actions //在rStore.actions中获取testStore的actions
   //不同rstore中的actions在props.actions以rstore名称命名
  return (
    <div>
      <input type="text" placeholder='用户名' onInput={({ target }) => {
        userData.id = target.value
      }} />
      <input type="text" placeholder='密码' onInput={({ target }) => {
        userData.password = target.value
      }} />
      <button onClick={() => { action.reqUserData(userData.id, userData.password) }}>登录</button>
      调用名为testStore的rstore中名为reqUserData的actions，来让rstore以预定的方法进行数据的更改，避免不应该出现的错误
    </div>
  )
}
export default creatProxy(App, ['testStore'])
```

### 普通组件中使用

#### 函数式
```javascript
import store from './testStore/store'

const App = () => {
  const { actions } = store
  const userData = {
    id: '',
    password: ''
  }
  return (
    <div>
      <input type="text" placeholder='用户名' onInput={({ target: { value } }) => {
        userData.id = value
      }} />
      <input type="text" placeholder='密码' onInput={({ target: { value } }) => {
        userData.password = value
      }} />
      <button onClick={() => { actions.reqUserData(userData.id, userData.password) }}>登录</button>
    </div>
  )
}
export default App
```


#### 类式
```javascript
import store from './testStore/store'
import React, { Component } from 'react'

export default class App extends Component {
  userData = {
    id: '',
    password: ''
  }
  render() {
    const { actions } = store
    return (
      <div>
        <input type="text" placeholder='用户名' onInput={({ target: { value } }) => {
          this.userData.id = value
        }} />
        <input type="text" placeholder='密码' onInput={({ target: { value } }) => {
          this.userData.password = value
        }} />
        <button onClick={() => { actions.reqUserData(this.userData.id, this.userData.password) }}>登录</button>
      </div>
    )
  }
```
