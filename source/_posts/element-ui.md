---
title: element-ui
date: 2019-12-05 13:49:37
tags:
- elementui
---

1、el-upload 组件
- element-ui的上传组件中的accept参数怎么使用<https://segmentfault.com/q/1010000010732163>
- element-ui文件上传 做类型大小的限制<https://www.cnblogs.com/yysbolg/p/11095901.html>

```js
// 上传之前验证是否是图片
beforeUpload(file) {
  const extension = file.name.substring(file.name.lastIndexOf(".") + 1);
  const types = ['jpg', 'jpeg', 'png', 'gif']
  types.includes(extension);
  return types.includes(extension);
}
```

限制上传文件大小

2、el-radio
```js
// 单选按钮选中事件处理函数，事件参数的值是选中的label的值
onRadioChange(e) {}
```

3、el-input 添加按下enter键的事件处理无效，添加 .native 修饰符之后可以了。

4、Vue组件的三种调用方式<https://www.cnblogs.com/wwhhq/p/8283769.html>

有的时候，有一些组件内部有很多的控制参数，这些参数控制着组件内部一些子组件的显示和隐藏，很多时候，这些用来实现显示控制的参数可能父组件实际要用到的参数还要多。

5、
在路由配置文件里面的配置meta参数，在组件内部可以通过 this.$route.meta 获取。

模态框既可以显式调用，又可以隐式调用。

仿 bootstrap 模态框

6、vue-element-admin 中使用svg图标。
框架中已经帮我们封装好了组件，只需要把需要的图标放进 `@/icon/svg` 文件夹内，在需要使用图标的地方引入 svg-icon 组件，

```html
<svg-icon icon-class="question-mark"></svg-icon>
```

icon-class 是放入`@/icon/svg`目录内的文件名称，
