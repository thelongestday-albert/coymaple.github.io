select 标签内的子标签 为 option 单词末尾没有s。

input:checkbox 表单复选框设置选中时，只需让checked出现在标签的属性中，默认选中的值为 checked="checked"，但是只要 checked 属性出现在标签中就可。 

默认情况下，设置了大小之后再为元素设置padding时，会使元素变大，默认情况下，元素的宽高指定的是元素内容区域的大小。
box-sizing: border-box; 可以改变它。

给予textarea初始值的话，要将内容包裹在标签体内部，不用为 value 属性赋值。

name 是 input 标签特有的属性，form表单提交时作为传递给后端的数据的键名。
label 与 input 关联。
```html
<label for="myInput">MyInput</label>
<input id="myInput" />
```
关联后，点击label等效于点击绑定的input元素。

:checked input 选中的元素 radio和checkbox特有。


获取dom节点后，可以通过 classlist 属性添加或删除类选择器。
```html
<div class="tab></div>
var div = document.querySelector('.tab');
div.classlist.add('active') // 添加类名
div.classlist.remove('active') // 删除类名
```

css开关按钮。

background-position: x y; (x,y为数值)
x为负值时，背景图片向左偏移，也就是元素向右偏移。
雪碧图定位

7、浏览器隐藏滚动条
```css
::-webkit-scrollbar {
  display: none;
}
```

2、返回上一页并刷新

`window.location.href=document.referrer`。referrer属性简单来说就是上一个页面的URL。

`history.back()` 和 `history.go(-1)` 虽然可以返回上一页，但并不会刷新该页面数据。

- 深入理解document.referrer的用法<https://www.cnblogs.com/cangqinglang/p/11323380.html>

4、表单中检验输入框中的是否为金额 `pattern="^[\d]*(\.[\d]{1,2})?$`

2、关于页面有多个叠加层，上面的覆盖下面的，导致下面的元素无法响应点击事件的处理方法。

为上层元素设置css属性，`pointer-events: none;`，这样上层元素就不会响应任何事件，顺便也可以让点击事件穿透该层，到达下层元素。

让上层元素也可以响应点击事件，`pointer-events: auto;`

3、绝对布局会影响 transform 变换的起点（transform-origin）。