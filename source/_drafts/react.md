jsx 内除可以写与html标签类似的代码以外，还可以使用 `{}` 来写js表达式。

jsx内不能写js代码，只能写表达式，所以if和for语句都不能写，循环遍历用数组的map函数，条件渲染不能用if语句。

条件渲染dom节点 `flag && <node />`，当flag 为true时可以将 node节点渲染出来，当flag为false时，node节点不会被渲染到页面上。
条件渲染还可以使用三目运算符和if判断。

列表渲染，jsx的列表渲染借助函数的 map 方法。

比如批量渲染图片，在map函数的lambda表达式内部，需要显示或隐式的返回。如果不返回不能将标签正确地渲染到页面中去。

```jsx
arr.map(item => {
  return (
    <img src={item.src}>
  )
})
// or
arr.map(item => <img src={item.src}>)
```

事件处理绑定 this 。

<!-- 事件对象参数 -->
onClick={(e) => this.onClickFunction(e)} 