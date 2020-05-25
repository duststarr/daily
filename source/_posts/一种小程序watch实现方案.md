---
title: 一种小程序watch实现方案
date: 2020-05-25 17:03:40
tags:
---
小程序没有全局变量的watch，很是不便

这里提供一种思路

## 第一步：在app.js中加入两个函数
```javascript
//app.js

  /**
   * 手动触发更新
   * @param {*} name 
   */
  globalEmit: function (name) {
    if (this.globalData._watches && this.globalData._watches[name]) {
      this.globalData._watches[name].forEach(func => {
        func(this.globalData[name])
      })
    }
  },
  /**
   * 订阅全局变量的变更
   * @param {*} name 
   * @param {*} callback 
   * @param {*} callAtonce 如果有值是否立即回调
   */
  globalWatch: function (name, callback, callAtonce = true) {
    if (!this.globalData._watches)
      this.globalData._watches = {}
    if (!this.globalData._watches[name])
      this.globalData._watches[name] = []
    this.globalData._watches[name].push(callback)
    if (callAtonce && this.globalData[name])
      callback(this.globalData[name])
  }
```

## 第二步：需要watch的地方
```javascript

getApp().globalWatch('globalVariableName',value=>{

})
```
## 第三步：变量更新后
```javascript
const app = getApp()

app.globalVariableName = ...
app.globalEmit('globalVariableName') // 手动触发更新事件
```

# 备注
最开设计是globalGet,globalSet,globalWatch，
后来发现全局变量是对象时，不好处理。
所以用globalEmit,globalWatch两个函数实现。
毕竟是临时方法，全局watch官方早晚会提供的