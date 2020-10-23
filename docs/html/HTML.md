## 对 HTML 语义化的理解

**语义化**：指对文本内容的结构化（内容语义化），选择合乎语义的标签（代码语义化）。

- 用正确的标签做正确的事情。
- html 语义化让页面的内容结构化，结构更清晰，便于对浏览器、搜索引擎解析;
- 即使在没有样式 CSS 情况下也以一种文档格式显示，并且是容易阅读的;
- 搜索引擎的爬虫也依赖于 HTML 标记来确定上下文和各个关键字的权重，利于 SEO;
- 使阅读源代码的人对网站更容易将网站分块，便于阅读维护理解。



## HTML标签里的css、js属性及作用

### 引入外联CSS样式方式

<link> 标签定义文档与外部资源的关系。最常见的用途是链接样式表。也可以被用来创建站点图标(比如PC端的“favicon”图标和移动设备上用以显示在主屏幕的图标) 。link 元素是空元素，它仅包含属性。此元素只能存在于 head 部分，不过它可出现任何次数。

```css
/* 第一种 链接方式*/
<link rel="stylesheet" type="text/css" href="sheet1.css" media="screen and (max-width: 600px)" title="Default" crossorigin="anonymous"/>
/* 网站图标的链接 */
<link rel="icon" href="favicon.ico">

/* 第二种 导入方式*/
<style type="text/css">
  @import url(styles.css); /* @import comes first */
  h1 {
    color: gray;
  }
</style>
```

> ###### 属性及属性值的含义
>
> `rel` 代表“关系”，规定当前文档与被链接文档之间的关系。stylesheet关系是指样式表。
>
> `type`属性类型总是设置为 text/css。此值描述将使用链接标记加载的数据类型。
>
> `href `属性的值是样式表的 URL。这个 URL 可以是绝对的，也可以是相对的。
>
> `media`媒体属性。此属性的值是一个或多个媒体描述符，它们是关于媒体类型和这些媒体特性的规则，每个规则由逗号分隔。例如，你可以在屏幕和投影媒体中使用`media="screen, projection"`。
>
> `title` 属性的值来生成样式替代列表。
>
> `crossorigin`属性表示在加载相关资源时是否必须使用 CORS。`"anonymous"`会发起一个跨域请求(即包含 `Origin:` HTTP 头). 但不会发送任何认证信息。
>
> `"use-credentials"`会发起一个带有认证信息的跨域请求 (即包含 `Origin:` HTTP 头). 
>
> `sizes` 定义了包含相应资源的可视化媒体中的icons的大小。
>
> **`as`**属性仅在`<link>`元素设置了 `rel="preload"` （表示浏览器应该预加载该资源 ）时才能使用。`as`属性表示获取特定的内容类。

###### 页面导入样式时，使用 link 和@import 有什么区别？

- link 属于 HTML，通过 **<link>** 标签中的 href 属性来引入外部文件，而 **@import** 属于 CSS，所以导入语句应写在 CSS 中，要注意的是导入语句应写在样式表的开头，否则无法正确导入外部文件；
- **@import** 是 CSS2.1 才出现的概念，所以如果浏览器版本较低，无法正确导入外部样式文件；
- 当 HTML 文件被加载时，link 引用的文件会同时被加载，而 **@import** 引用的文件则会等页面全部下载完毕再被加载；

### 引入js代码方式

<script></script> 元素用于嵌入或引用可执行脚本。这通常用作嵌入或者指向 JavaScript 代码。  

```js
<script type="text/javascript" async  defer crossorigin="anonymous"src="javascript.js">
```

> **`src`** 属性定义引用外部脚本的URI，这可以用来代替直接在文档中嵌入脚本。
>
> **`type`** 属性定义script元素包含或`src`引用的脚本语言。属性的值为MIME类型; 支持的MIME类型包括`text/javascript`, `text/ecmascript`, `application/javascript`, 和`application/ecmascript`。如果没有定义这个属性，脚本会被视作JavaScript。**如果type属性为`module`，代码会被当作JavaScript模块。**
>
> `charset`规定在外部脚本文件中使用的字符编码。
>
> **`async`** 属性对于普通脚本，如果存在 `async` 属性，那么普通脚本会被并行请求，并尽快解析和执行。
>
> **`crossorigin`**属性可以使那些将静态资源放在另外一个域名的站点打印错误信息。
>
> **`defer`**被设定用来通知浏览器该脚本将在文档完成解析后，触发 `DOMContentLoaded` 事件前执行。


## html的head包含内容
```html
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
	<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
	<meta name="Author" content="">
    <meta name="Keywords" content="厉害很厉害" />
    <meta name="Description" content="网易是中国领先的互联网技术公司，为用户提供免费邮箱、游戏、搜索引擎服务，开设新闻、娱乐、体育等30多个内容频道，及博客、视频、论坛等互动交流，网聚人的力量。" />
  <meta http-equiv="refresh" content="3;http://www.baidu.com">  
  <title>Document</title>
</head>
```

头标签内部的常见标签如下：

 - `<title>`：指定整个网页的标题，在浏览器最上方显示。

 - `<base>`：为页面上的所有链接规定默认地址或默认目标。

 - `<meta>`：提供有关页面的基本信息。

    - （1）字符集 charset：

      ```html
      <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
      ```

      字符集用meta标签中的`charset`定义，charset就是charactor set（即“字符集”），即**网页的编码方式**

      （2）视口 viewport：

      ```html
          <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1,maximum-scale=1,user-scalable=no">
      ```

      `width=device-width` ：表示视口宽度等于屏幕宽度。

      - initial-scale ：初始缩放比例。
      - minimum-scale ： 最小缩放比例。
      - maximum-scale ： 最大缩放比例。
      - user-scalable ：是否允许用户缩放。

   - （3）定义“关键词”name：

     举例如下：

     ```html
     <meta name="Keywords" content="网易,邮箱,游戏,新闻,体育,娱乐,女性,亚运,论坛,短信" />
     ```

     这些关键词，就是告诉搜索引擎，这个网页是干嘛的，能够提高搜索命中率。让别人能够找到你，搜索到你。

   - （4）定义“页面描述”：

     meta除了可以设置字符集，还可以设置关键字和页面描述。

     只要设置Description页面描述，那么百度搜索结果，就能够显示这些语句，这个技术叫做**SEO**（search engine optimization，搜索引擎优化）。

     设置页面描述的举例：

     ```html
     <meta name="Description" content="网易是中国领先的互联网技术公司，为用户提供免费邮箱、游戏、搜索引擎服务，开设新闻、娱乐、体育等30多个内容频道，及博客、视频、论坛等互动交流，网聚人的力量。" />
     ```

   - （5）自动跳转：

     ```html
     <meta http-equiv="refresh" content="3;http://www.baidu.com">
     ```

     上面这个标签的意思是说，3秒之后，自动跳转到百度页面。

 - `<body>`：用于定义HTML文档所要显示的内容，也称为主体标签。我们所写的代码必须放在此标签內。

 - `<link>`：定义文档与外部资源的关系。

    - ```js
      <link rel="stylesheet" href="xxx.css" type="text/css">
      ```

* `<style>`：包含文档的样式信息。

* `<script>`：用于嵌入或引用可执行脚本。

  * `async`：使浏览器使用另一个线程下载脚本，这时不会阻塞页面渲染。当脚本下载完成后，浏览器会暂停渲染，执行脚本，执行完毕后继续渲染页面。**async 无法保证脚本的执行顺序，哪个脚本先下载结束就会先执行。**

  * `defer`：同样会使浏览器并行下载脚本，但是下载完毕不会立即执行，而是会等到 DOM 加载完成后再执行脚本。**defer 可以保证脚本的执行顺序就是它们在页面上出现的顺序。**

  * `src`：定义引用外部脚本的地址，指定此属性的 script 标签内不应再有嵌入的脚本。如果脚本文件使用了非英语字符，还应该注明字符的编码。如：

    ```js
    <script charset="utf-8" src="https://www.example.com/script.js"></script>
    ```

  * `type`：默认值是 text/javascript

* `<noscript>`：如果页面上的脚本类型不受支持或者当前在浏览器中关闭了脚本，则在此中定义脚本未被执行时的替代内容。

> 链接：https://juejin.im/post/6844903800956796941

## `<a></a>`标签全部作用

```css
<a href=""></a>
	href="" //刷新页面
	href="#"//跳转到当前页面的顶部（不会刷新）
	href="javascript:void(0)" // 什么都不做
	href="javascript:;" // 什么都不做
```

## 行内元素和块级元素区别

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

## nodeType值及代表含义

- nodeType == 1 表示的是元素节点（标签）。
- nodeType == 2 表示是属性节点。
- nodeType == 3 是文本节点。

获取相应节点的实例：

```html
<body>
    <div id="box" value="111">hello</div>
    <script>
        var element = document.getElementById("box"); // 获取元素节点（标签）
        var attributeNode = element.getAttributeNode("id"); //获取box的属性节点
        var textNode = element.firstChild; // 获取box的文本节点
        console.log(element); // <div id="box" value="111">hello</div>
        console.log(attributeNode); // id="box"
        console.log(textNode); // "hello"
        // 获取nodeType
        console.log(element.nodeType); //1
        console.log(attributeNode.nodeType); //2
        console.log(textNode.nodeType); //3
        // 获取nodeName
        console.log(element.nodeName); //DIV
        console.log(attributeNode.nodeName); //id
        console.log(textNode.nodeName); // #text
        // 获取nodeValue
        console.log(element.nodeValue); //null
        console.log(attributeNode.nodeValue); //box
        console.log(textNode.nodeValue); // hello
    </script>
</body>
```

## html添加注释

html注释：

**注释标签 <!-- 与 --> **

`注释//`

条件注释：

```html
<!--[if IE 8]>
    .... some HTML here ....
<![endif]-->
```

## meta viewport使用

```html
 	<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
```

控制页面在移动端不要缩小显示。

## Doctype 作用及区别

**概念**：`DOCTYPE` 是用来声明文档类型和 DTD （DocType Declaration）规范的。任何一个标准的HTML页面，第一行一定是一个以`<!DOCTYPE ……>`开头的语句。它不是一个 HTML 标签，处于 html 标签之前。**DTD可告知浏览器文档使用哪种 HTML 或 XHTML 规范**。

**作用**：告知浏览器的解析器用什么文档标准解析这个文档。DOCTYPE 不存在或格式不正确会导致文档以兼容模式呈现。

**标准模式与兼容模式区别**：标准模式的排版 和 JS 运作模式都是以该浏览器支持的最高标准运行。在兼容模式中，页面以宽松的向后兼容的方式显示,模拟老式浏览器的行为以防止站点无法工作。

在 HTML4.01 中<!doctype>声明指向一个 DTD，由于 HTML4.01 基于 SGML，所以 DTD 指定了标记规则以保证浏览器正确渲染内容 HTML5 不基于 SGML，所以不用指定 DTD。

#### 补充：

**HTML4.01**这个版本是IE6开始兼容的。**HTML5是IE9开始兼容的**。如今，手机、移动端的网页，就可以使用HTML5了，因为其兼容性更高。

HTML4.01里面有两大种规范，每大种规范里面又各有3种小规范。所以一共6种规范（见下图）。

![](http://img.smyhvae.com/20170629_1600.png)

> 总结：
>
> - 让浏览器以标准模式渲染
> - 让浏览器知道元素的合法性

## HTML、XHTML、HTML5的区别

- HTML（HyperText Markup Language） 是超文本标记语言，负责描述文档语义的语言，属于 SGML。
- XHTML （Extensible Hypertext Markup Language）是可扩展超文本标记语言，属于 XML，是 HTML 进行 XML 严格化的结果。
- HTML5 不属于SGML，也不属于 XML（HTML5有自己独立的一套规范），比 XHTML 宽松。

## HTML 全局属性

全局属性是所有 HTML 元素共有的属性; 它们可以用于所有元素，即使属性可能对某些元素不起作用。

[全局属性 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes)

##  link 和@import 区别

- link 属于 XHTML 标签，除了加载 CSS 外，还能用于定义 RSS, 定义 rel 连接属性等作用；而@import 是 CSS 提供的，只能用于加载 CSS;
- 页面被加载的时，link 会同时被加载，而@import 引用的 CSS 会等到页面被加载完再加载;
- import 是 CSS2.1 提出的，只在 IE5 以上才能被识别，而 link 是 XHTML 标签，无兼容问题;
- link 支持使用 js 控制 DOM 去改变样式，而@import 不支持;

## 如何实现浏览器内多个标签页之间的通信? (阿里)

- WebSocket、SharedWorker；
- 也可以调用 localstorge、cookies 等本地存储方式；

localstorge 另一个浏览上下文里被添加、修改或删除时，它都会触发一个`storage`事件，

我们通过监听事件，控制它的值来进行页面信息通信；

注意 quirks：Safari 在无痕模式下设置 localstorge 值时会抛出 QuotaExceededError 的异常；

## webSocket 如何兼容低浏览器？(阿里)

- Adobe Flash Socket 、
- ActiveX HTMLFile (IE) 、
- 基于 multipart 编码发送 XHR 、
- 基于长轮询的 XHR

## title 与 h1 的区别、b 与 strong 的区别、i 与 em 的区别？

- title 属性没有明确意义只表示是个标题，H1 则表示层次明确的标题，对页面信息的抓取也有很大的影响；
- strong 是标明重点内容，有语气加强的含义，使用阅读设备阅读网络时：strong 会重读，而 b 是展示强调内容。
- i 内容展示为斜体是样式标签，em 表示强调的文本，是语义化标签；效果都是斜体



Physical Style Elements -- 自然样式标签

b, i, u, s, pre

Semantic Style Elements -- 语义样式标签

strong, em, ins, del, code

应该准确使用语义样式标签, 但不能滥用, 如果不能确定时首选使用自然样式标签。

## 哪些元素可以自闭合？

- 表单元素 input
- img
- br, hr
- meta, link

## HTML 和 DOM 的关系

- HTML 只是一个字符串
- DOM 由 HTML 解析而来
- JS 可以维护 DOM

## form 作用

- 直接提交表单
- 使用 submit / reset 按钮
- 便于浏览器保存表单
- 第三方库（比如 jQuery）可以整体获取值
- 第三方库可以进行表单验证

所以，如果我们是通过 Ajax 提交表单数据，也建议加上 form。

## html 中 title 属性和 alt 属性的区别？

```
<img src="#" alt="alt信息" />
```

当图片不输出信息的时候，会显示 alt 信息 鼠标放上去没有信息，当图片正常读取，不会出现 alt 信息。

```
<img src="#" alt="alt信息" title="title信息" />
```

- 当图片不输出信息的时候，会显示 alt 信息 鼠标放上去会出现 title 信息；
- 当图片正常输出的时候，不会出现 alt 信息，鼠标放上去会出现 title 信息。
- 除了纯装饰图片外都必须设置有意义的值，搜索引擎会分析。

#### 另外还有一些关于 title 属性的知识：

- title 属性可以用在除了 base，basefont，head，html，meta，param，script 和 title 之外的所有标签。
- title 属性的功能是提示。额外的说明信息和非本质的信息请使用 title 属性。title 属性值可以比 alt 属性值设置的更长。
- title 属性有一个很好的用途，即为链接添加描述性文字，特别是当连接本身并不是十分清楚的表达了链接的目的。

## 为什么我们要弃用 table 标签？

table 的缺点在于服务器把代码加载到本地服务器的过程中，本来是加载一行执行一行，但是 table 标签是里面的东西**全都下载完之后才会显示出来**，那么如果图片很多的话就会导致网页一直加载不出来，除非所有的图片和内容都加载完。如果要等到所有的图片全都加载完之后才显示出来的话那也太慢了，所以 table 标签现在我们基本放弃使用了。

## iframe 有那些缺点？

- iframe 会阻塞主页面的 Onload 事件；
- 搜索引擎的检索程序无法解读这种页面，不利于 SEO;
- iframe 和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。

使用 iframe 之前需要考虑这两个缺点。如果需要使用 iframe，最好是通过 javascript

动态给 iframe 添加 src 属性值，这样可以绕开以上两个问题。

## Label 的作用是什么？是怎么用的？

label 标签来定义表单控制间的关系,当用户选择该标签时，浏览器会自动将焦点转到和标签相关的表单控件上。

## 页面可见性（Page Visibility API） 可以有哪些用途？

- 通过 visibilityState 的值检测页面当前是否可见，以及打开网页的时间等;
- 在页面被切换到其他后台进程的时候，自动暂停音乐或视频的播放；

## 如何在页面上实现一个圆形的可点击区域？

- map+area 或者 svg
- border-radius
- 纯 js 实现 需要求一个点在不在圆上简单算法、获取鼠标坐标等等

## 实现不使用 border 画出 1px 高的线，在不同浏览器的标准模式与怪异模式下都能保持一致的效果。

```html
<div style="height:1px;overflow:hidden;background:red"></div>
```

## 移动端项目需要注意的 4 个问题

#### meta 中设置 viewport

阻止用户手滑放大或缩小页面，需要在 index.html 中添加 meta 元素,设置 viewport。

```
<meta
  name="viewport"
  content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no"
/>
```

#### CSS 样式统一问题

我们需要重置页面样式，因为在不同的手机浏览器上，默认的 css 样式不是统一的。 解决方法：使用 reset.css 重置所有元素的默认样式

#### 一像素边框问题

有的手机分辨率比较高，是 2 倍屏或 3 倍屏，手机上的浏览器就会把 CSS 中的 1 像素值展示为 2 个或 3 个物理宽度 解决方法： 添加一个 border.css 库，将利用**scroll 缩放的原理**将边框重置。当我们需要使用一像素边框时只需要在标签上添加对应类名，如设置底部一像素边框就在标签上加入"border-bottom"的 class 名

#### 300 毫秒点击延迟问题

在移动端开发中，某些机型上使用 click 事件会延迟 300ms 才执行，这样影响了用户体验。 解决方法： 引入[fastclick.js](https://www.jianshu.com/p/05b142d84780)。

#### innerHTML和innerText的区别

- value：标签的value属性。

- **innerHTML**：双闭合标签里面的内容（包含标签）。

- **innerText**：双闭合标签里面的内容（不包含标签）。（老版本的火狐用textContent）

**区别**：

获取内容：innerHTML会获取到标签本身，而innerText则不会

修改内容：innerHTML会修改标签本身，而innerText则不会

## documen.write 和 innerHTML 的区别

- document.write 只能重绘整个页面
- innerHTML 可以重绘页面的一部分

## innerHTML 与 outerHTML 的区别？

DOM 元素的 `innerHTML`, `outerHTML`, `innerText`, `outerText` 属性的区别也经常被面试官问到， 比如对于这样一个HTML元素：`<div>content<br/></div>`。

- `innerHTML`：内部HTML，`content<br/>`；
- `outerHTML`：外部HTML，`<div>content<br/></div>`；
- `innerText`：内部文本，`content `；
- `outerText`：内部文本，`content `；

上述四个属性不仅可以读取，还可以赋值。`outerText` 和 `innerText` 的区别在于 `outerText` 赋值时会把标签一起赋值掉，另外 `xxText` 赋值时HTML特殊字符会被转义。

## jQuery的html()与innerHTML的区别？

jQuery的 `.html()` 会调用`.innerHTML`来操作，但是会捕获异常，然后用 `.empty()`, `.append()` 重新操作。 这是因为IE8中有些元素的 `.innerHTML` 是只读的。见：http://stackoverflow.com/questions/3563107/jquery-html-vs-innerhtml

## Window 对象 与 document 对象

##### window

- Window 对象表示当前浏览器的窗口，是 JavaScript 的顶级对象。
- 我们创建的所有对象、函数、变量都是 Window 对象的成员。
- Window 对象的方法和属性是在全局范围内有效的。

##### document

- Document 对象是 HTML 文档的根节点与所有其他节点（元素节点，文本节点，属性节点, 注释节点）
- Document 对象使我们可以通过脚本对 HTML 页面中的所有元素进行访问
- Document 对象是 Window 对象的一部分，即 window.document

## 客户区坐标、页面坐标、屏幕坐标区别

客户区坐标：**鼠标指针在可视区中**的水平坐标(clientX)和垂直坐标(clientY)

页面坐标：**鼠标指针在页面布局中**的水平坐标(pageX)和垂直坐标(pageY)

屏幕坐标：**设备物理屏幕的**水平坐标(screenX)和垂直坐标(screenY)

## mouseover/mouseout 与 mouseenter/mouseleave 的区别与联系

1. mouseover/mouseout 是标准事件，**所有浏览器都支持**；mouseenter/mouseleave 是 IE5.5 引入的特有事件后来被 DOM3 标准采纳，现代标准浏览器也支持
2. mouseover/mouseout 是**冒泡**事件；mouseenter/mouseleave**不冒泡**。需要为**多个元素监听鼠标移入/出事件时，推荐 mouseover/mouseout 托管，提高性能**
3. 标准事件模型中 event.target 表示发生移入/出的元素,**vent.relatedTarget**对应移出/如元素；在老 IE 中 event.srcElement 表示发生移入/出的元素，**event.toElement**表示移出的目标元素，**event.fromElement**表示移入时的来源元素

## focus/blur 与 focusin/focusout 的区别与联系

1. focus/blur 不冒泡，focusin/focusout 冒泡
2. focus/blur 兼容性好，focusin/focusout 在除 FireFox 外的浏览器下都保持良好兼容性，如需使用事件托管，可考虑在 FireFox 下使用事件捕获 elem.addEventListener('focus', handler, true)
