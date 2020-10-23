## HTML篇

### 行内元素与块级元素比较

| 行内元素（内联元素）                                         | 块级元素                                                     | 行内块元素（置换/替换元素）    |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------ |
| `display:inline;`                                            | `display:block;`                                             | `display:inline-block;`        |
| `a, b, em, span, select, button, br, label, strong `         | `div，p，h1-h6,  table,  ul,  form,  hr,  article，aside,  header,  video,  dl,  ol` | `input、img`                   |
| 不能设置高宽，它的**高度取决于内部文字的行高。宽度取决于内部文字的多少** | 可以设置width和height                                        | 同块级元素一致                 |
| 只能设置margin、padding的left、right元素，不可以设置top，bottom等元素 | 可以设置margin和padding                                      | 同块级元素一致                 |
| 相邻的行内元素会排在同一行，直到一行排不下才会换行，其宽度随内容变化 | 独占一行，在默认情况下，其宽度会自动填满父级元素宽度         | 允许左右存在元素，不是独占一行 |

通常 **HTML 的层级结构**允许**行内元素作为块级元素的后代**，而不是相反，**CSS 则没有这样的限制，你可以保持现有的标签结构**，然后修改这两个元素的显示角色。

```html
<body>
  <p>This is a paragraph with <em>an inline element</em> within it.</p>
</body>
<style>
p {
  display: inline;
}
em {
  display: block;
}
</style>
```

### 浏览器数据存储

#### cookie、localStorage、seesionStorage、IndexedDB区别

| cookie                   | localStorage | seesionStorage | IndexedDB |
| ------------------------ | ------------ | -------------- | --------- |
| 在客户端用于存储会话信息 |              |                |           |
|                          |              |                |           |
|                          |              |                |           |

- cookie 是网站为了标示用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）
- cookie 数据始终在同源的 http 请求中携带（即使不需要），记会在浏览器和服务器间来回传递。
- sessionStorage 和 localStorage 不会自动把数据发给服务器，仅在本地保存。
- 存储大小：
  - cookie 数据大小不能超过 4k。
  - sessionStorage 和 localStorage 虽然也有存储大小的限制，但比 cookie 大得多，可以达到 5M 或更大。
- 有效期（生命周期）：
  - localStorage: 存储持久数据，浏览器关闭后数据不丢失除非主动删除数据；
  - sessionStorage: 数据在当前浏览器窗口关闭后自动删除。
  - cookie: 设置的 cookie 过期时间之前一直有效，即使窗口或浏览器关闭
- 共享
  - sessionStorage 不能共享，localStorage 在同源文档之间共享，cookie 在同源且符合 path 规则的文档之间共享

###### localStorage和sessionStorage区别

`localStorage`和`sessionStorage`一样都是用来**存储客户端临时信息的对象**。

**他们均只能存储字符串类型的对象**（虽然规范中可以存储其他原生类型的对象，但是目前为止没有浏览器对其进行实现）。

**`localStorage`生命周期是永久**，这意味着除非用户主动在浏览器上清除`localStorage`信息，否则这些信息将永远存在。

**`sessionStorage`生命周期为当前窗口或标签页**，一旦窗口或标签页被永久关闭了，那么所有通过`sessionStorage`存储的数据也就被清空了。

不同浏览器无法共享`localStorage`或`sessionStorage`中的信息。相同浏览器的不同页面间可以共享相同的 `localStorage`（页面属于相同域名和端口），但是不同页面或标签页间无法共享`sessionStorage`的信息。这里需要注意的是，页面及标签页仅指顶级窗口，如果一个标签页包含多个`iframe`标签且他们属于同源页面，那么他们之间是可以共享`sessionStorage`的。

## JS篇

### call、apply、bind的区别

- 都能改变this的指向
- call()/apply()是**立即调用函数**
- bind()是将函数返回，因此后面还需要加`()`才能调用。
- `call()和apply()`可以实现继承

### undefined与null的区别？

**null表示"没有对象"，即该处不应该有值。**典型用法是：

> （1） 作为函数的参数，表示该函数的参数不是对象。
>
> （2） 作为对象原型链的终点。
>
> ```js
> Object.getPrototypeOf(Object.prototype) // null
> ```

**undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。**典型用法是：

> （1）变量被声明了，但没有赋值时，就等于undefined。
>
> （2) 调用函数时，应该提供的参数没有提供，该参数等于undefined。
>
> （3）对象没有赋值的属性，该属性的值为undefined。
>
> （4）函数没有返回值时，默认返回undefined。

```js
var i;
i // undefined
function f(x){console.log(x)}
f() // undefined
var o = new Object();
o.p // undefined
var x = f();
x // undefined
```

### 基本数据类型和引用数据类型的区别？

- 基本数据类型：
  - 参数赋值的时候，**传数值**。
  - 基本数据类型的值，直接保存在**栈内存**中。值与值之间是独立存在，修改一个变量不会影响其他的变量
- 引用数据类型：
  - 参数赋值的时候，**传地址**（修改的同一片内存空间）。
  - 对象是保存到**堆内存**中的。每创建一个新的对象，就会在堆内存中开辟出一个新的空间；而**变量保存了对象的内存地址**（对象的引用），保存在栈内存当中。**如果两个变量保存了同一个对象的引用，当一个通过一个变量修改属性时，另一个也会受到影响。**

### canvas 和 svg的区别，D3.js与echart.js的区别？（2）

Echarts：**echarts是使用canvas来绘制图形的，**封装好的方法可以直接调用；使用简单，但不够灵活，可以兼容到ie6以及以上的所有主流浏览器；**canvas不支持事件处理器**，所以只能展示数据，而不能修改。如果项目中一般没有用户交互的场景，**前端将数据通过图表的形式展示给用户**，一般使用echarts。

**D3：**：**D3是通过Svg来绘制图形的**，太底层，使用灵活，但学习成本大；兼容到IE9以上以及所有的主流浏览器；D3通过svg来绘制图形，可以自定义事件，想要实现某个操作，直接调用相关的方法实现效果就行，而且它存在**链式语法，和jQuery的链式调用差不多**，简单易读。如果在项目开发中如果有较多用户交互的场景，可以使用D3。

> **Svg**：不依赖分辨率；可以操作dom、支持事件处理器；复杂度高，会减慢页面的渲染速度
>
> **Canvas：**：依赖分辨率；基于js绘制图形、不支持事件处理器；能以png或者jpg的格式保存图片。