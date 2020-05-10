

前几天在一家公司面试的时候被问了一些与cookie和localStorage有关的面试题，没有答上来，有的甚至是从来没有听说过，比如说，localStorage的实现原理，这个该怎么答呢，难不成是在问我浏览器是怎么存储localStorage数据的。平日里localStorage用得比较频繁，但是他的实现原理真的不知道，当时甚至很想跟面试官理论一下前端不需要了解实现原理，拿来用就可以了嘛。

<!-- more! -->

## cookie

cookie 最早是由网景公司的程序员提出并实现，其主要针对在浏览器客户端（以下统称客户端）长时间存储用户标识，避免每次进入网站都要重复登录流程。

因为是很久以前就提出来的技术，因此，它也有很多弊端，比如说只能存储少于4k的数据，超出4k的数据会被丢弃；而且它有过期时间，一旦过期，数据就会被丢弃。另外，在客户端与服务器的通信过程中，cookie也会随着每次请求作为请求头的一部分一起被发送。

对于cookie，客户端采用了很多限制，以使得cookie看起来很安全，包括限制。

## localStorage & sessionStorage

因为cookie有着或这或那的缺点，以及随着技术的发展，前端程序员有着在客户端存储大量数据的需求，于是 Web Storage 应运而生。

<!-- 作为 Web Storage API 的接口，Storage 提供了访问特定域名下的会话存储或本地存储的功能，例如，可以添加、修改或删除存储的数据项。 -->

<!-- 第一种设计用于以下场景：用户正在执行一次交易，但可能同时在不同的窗口中执行多个交易。 -->

<!-- Cookies不能很好地处理这种情况。例如，用户可能在同一站点的两个不同窗口中购买机票。如果该站点使用Cookie来跟踪用户正在购买哪个票证，那么当用户在两个窗口中从一个页面到另一个页面单击时，当前正在购买的票证将从一个窗口“泄漏”到另一个窗口，从而可能导致用户买两张同一航班的机票而没有真正注意到。 -->

<!-- Storage 接口
WindowLocalStorage 对象
WindowSessionStorage 对象
每个站点都有独立的存储区域。 -->

<!-- https://html.spec.whatwg.org/multipage/webstorage.html#webstorage -->



官方定义了一个Storage对象，用来实现在客户端存储数据，并对数据进行管理，localStorage 和 sessionStorage 都是这个对象的一个实例，这两个实例都挂载在浏览器全局对象 windows 下面，因此可以直接通过属性名进行调用。两者的属性和方法也比较类似，所不同的是 localStorage 是持久性存储，只要不清理浏览器缓存或调用 removeItem 或 clear 方法，它存储的数据就一直有效；sessionStorage 只在调用它的当前会话窗口有效，一旦窗口关掉了，数据就会被清除掉。并且sessionStorage 不能跨会话窗口访问。

localStorage和sessionStorage可以使用的属性或方法如下：

storage.setItem(k,v) 往当前storage实例中存放 k,v数据

storage.getItem(k) 通过 k 获取对应的键值对值

storage.clear() 清理当前storage实例中保存的数据

storage.removeItem(k) 删除指定的键名k 对应的数据

storage.key(ind) 返回 ind 对应的键值对的键名

存储在Storage实例中的数据实际上是以类似于对象属性的方式存储的，因此：
除 setItem() 外，还可以使用与为对象属性赋值的方式添加数据；
同理，除getItem()，也可以使用对象属性调用方式获取数据值。

delete 关键字也可以用于删除单个数据。

for-in循环可以用于遍历Storage实例内的数据。

由于Storage只能存储字符串数据，其它类型的数据会被强制转换成字符串类型，如果要存储复杂类型如Object和Array类型的数据，可以搭配使用JSON.stringify() 和 JSON.parse() 函数。