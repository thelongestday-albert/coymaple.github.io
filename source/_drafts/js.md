jQuery 
添加节点 append() 该函数的参数可以是一段html字符串或 $(selector)
删除节点 $('#id').remove() 移除id为id的元素节点。
添加class类名 addClass(className)
移除class类名 removeClass(className)
监听事件 $(selector).on(eventName,callback)，在callback的回调函数中，事件参数的属性 target 和 currentTarger 与浏览器原生一样，这意味着不能使用jQuery的函数。
显示节点 show()
隐藏节点 hide()