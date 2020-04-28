---
title: Vue笔记
tags:
- vue
---

## vuex 持久化存储
```js
import createPersistedState from 'vuex-persistedstate'

const store = new Vuex.Store({
  // ...
  plugins: [createPersistedState()],
})
```

vuex的使用及持久化state的方式<https://www.cnblogs.com/ChengWuyi/p/8277136.html>

## 其他
让vue项目可通过localhost和IP同时访问，又避免启动打开页面为0.0.0.0的尴尬<https://blog.csdn.net/xhom_w/article/details/101674165>