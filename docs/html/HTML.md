## html了解的head里面包含哪些内容

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

## script放在 head种和body种的区别

##### 1、加载的顺序不一样

浏览器是从上到下解析HTML的。放在head里的js代码，会在body解析之前被解析；放在body里的js代码，会在整个页面加载完成之后解析。

放在body中：在页面加载的时候被执行

放在head中：在被调用时被执行

总结：**放入html的head,是页面加载前就运行，放入body中，则加载后才运行javascript的代码**

## meta viewport 是做什么用的，怎么写？

```html
 	<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
```

控制页面在移动端不要缩小显示。

## Doctype 是什么，有什么作用？标准模式与兼容模式各有什么区别?

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

## 行内元素有哪些？块级元素有哪些？ 空(void)元素有那些？

**定义**：CSS 规范规定，每个元素都有 display 属性，确定该元素的类型，每个元素都有默认的 display 值，如 div 的 display 默认值为“block”，则为“块级”元素；span 默认 display 属性值为“inline”，是“行内”元素。

- 行内元素有：a b span img input select strong（强调的语气）
- 块级元素有：div ul ol li dl dt dd h1 h2 h3 h4…p
- 空元素：
  - 常见: br hr img input link meta
  - 不常见: area base col command embed keygen param source track wbr

不同浏览器（版本）、HTML4（5）、CSS2 等实际略有差异 参考: http://stackoverflow.com/questions/6867254/browsers-default-css-for-html-elements

## HTML 全局属性

全局属性是所有 HTML 元素共有的属性; 它们可以用于所有元素，即使属性可能对某些元素不起作用。

[全局属性 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes)

## 页面导入样式时，使用 link 和@import 有什么区别？

- link 属于 XHTML 标签，除了加载 CSS 外，还能用于定义 RSS, 定义 rel 连接属性等作用；而@import 是 CSS 提供的，只能用于加载 CSS;
- 页面被加载的时，link 会同时被加载，而@import 引用的 CSS 会等到页面被加载完再加载;
- import 是 CSS2.1 提出的，只在 IE5 以上才能被识别，而 link 是 XHTML 标签，无兼容问题;
- link 支持使用 js 控制 DOM 去改变样式，而@import 不支持;

## HTML5 变化

HTML5 现在已经不是 SGML 的子集，主要是关于图像，位置，存储，多任务等。 

### 新的语义化元素

##### 1、H5中新增的语义标签

- `<section>` 表示区块

- `<article>` 表示文章。如文章、评论、帖子、博客

- `<header>` 表示页眉

- `<footer>` 表示页脚

- `<nav>` 表示导航

- `<aside>` 表示侧边栏。如文章的侧栏

- <figure> 表示媒介内容分组。

- `<mark>` 表示标记 (用得少)

- `<progress>` 表示进度 (用得少)

- `<time>` 表示日期

本质上新语义标签与`<div>`、`<span>`没有区别，只是其具有表意性，使用时除了在HTML结构上需要注意外，其它和普通标签的使用无任何差别，可以理解成`<div class="nav">` 相当于`<nav>`。

##### 2、移除的元素

- 纯表现的元素：basefont，big，center，font, s，strike，tt，u;
- 对可用性产生负面影响的元素：frame，frameset，noframes；

### 表单增强

- `email` 只能输入email格式。自动带有验证功能。
- `tel` 手机号码。
- `url` 只能输入url格式。
- `number` 只能输入数字。
- `search` 搜索框
- `range` 滑动条
- `color` 拾色器
- `time` 时间
- `date` 日期
- `datetime` 时间日期
- `month` 月份
- `week` 星期

上面的部分类型是针对移动设备生效的，且具有一定的兼容性，在实际应用当中可选择性的使用。

### 新 API

- 离线 （applicationCache ）

- 音视频 （audio, vidio）

- 图形 （canvans）

- 实时通信（websoket）

- 本地存储（本地离线存储 localStorage 长期存储数据，浏览器关闭后数据不丢失；sessionStorage 的数据在浏览器关闭后自动删除）

- 设备能力（地图定位，手机摇一摇）

- 支持 HTML5 新标签：

  - IE8/IE7/IE6 支持通过 document.createElement 方法产生的标签，

  - 可以利用这一特性让这些浏览器支持 HTML5 新标签，

  - 浏览器支持新标签后，还需要添加标签默认的样式。

  - 当然也可以直接使用成熟的框架、比如 html5shim;

    ```
    <!--[if lt IE 9]>
      <script>
        src = "http://html5shim.googlecode.com/svn/trunk/html5.js";
      </script>
    <![endif]-->
    ```

- 如何区分 HTML5： DOCTYPE 声明\新增的结构元素\功能元素

## HTML5 的 form 如何关闭自动补全功能？

给不想要提示的 form 或某个 input 设置为 autocomplete=off。

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

## HTML5 的离线储存怎么使用，工作原理能不能解释一下？

在用户没有与因特网连接时，可以正常访问站点或应用，在用户与因特网连接时，更新用户机器上的缓存文件。

原理：HTML5 的离线存储是基于一个新建的.appcache 文件的缓存机制(不是存储技术)，通过这个文件上的解析清单离线存储资源，这些资源就会像 cookie 一样被存储了下来。之后当网络在处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示。

#### `cache manifest` 缓存清单文件

缓存清单文件中列出了浏览器应缓存，以供离线访问的资源。推荐使用 `.appcache`作为后缀名，另外还要添加MIME类型。

**缓存清单文件里的内容怎样写：**

（1）顶行写CACHE MANIFEST。

（2）CACHE: 换行 指定我们需要缓存的静态资源，如.css、image、js等。

（3）NETWORK: 换行 指定需要在线访问的资源，可使用通配符（也就是：不需要缓存的、必须在网络下面才能访问的资源）。

（4）FALLBACK: 换行 当被缓存的文件找不到时的备用资源（当访问不到某个资源时，自动由另外一个资源替换）。

格式举例1：

![](http://img.smyhvae.com/20180224_2240.png)


格式举例2：

```bash
CACHE MANIFEST

#要缓存的文件
CACHE:
    images/img1.jpg
    images/img2.jpg


#指定必须联网才能访问的文件
NETWORK:
     images/img3.jpg
     images/img4.jpg


#当前页面无法访问是回退的页面
FALLBACK:
    404.html

```


**缓存清单文件怎么用：**

（1）例如我们创建一个名为 `demo.appcache`的文件。例如：

demo.appcache：

```bash
CACHE MANIFEST

# 注释以#开头
#下面是要缓存的文件
CACHE:
    http://img.smyhvae.com/2016040101.jpg
```


（2）在需要应用缓存在页面的根元素(html)里，添加属性manifest="demo.appcache"。路径要保证正确。例如：


```html
<!DOCTYPE html>
<html manifest="01.appcache">
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<img src="http://img.smyhvae.com/2016040101.jpg" alt=""/>
</body>
</html>
```

### 浏览器是怎么对 HTML5 的离线储存资源进行管理和加载的呢？

- 在线的情况下，浏览器发现 html 头部有 manifest 属性，它会请求 manifest 文件，如果是第一次访问 app，那么浏览器就会根据 manifest 文件的内容下载相应的资源并且进行离线存储。如果已经访问过 app 并且资源已经离线存储了，那么浏览器就会使用离线的资源加载页面，然后浏览器会对比新的 manifest 文件与旧的 manifest 文件，如果文件没有发生改变，就不做任何操作，如果文件改变了，那么就会重新下载文件中的资源并进行离线存储。
- 离线的情况下，浏览器就直接使用离线存储的资源。

在离线状态时，操作 window.applicationCache 进行需求实现。

参考链接：[HTML5 离线缓存-manifest 简介](https://yanhaijing.com/html/2014/12/28/html5-manifest/)

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

## property 和 attribute 的区别

例如一个 input 标签 `<input value="3" />` 它的 attribute 是 3 但如果使用`input.value = 4` 或 直接修改值为 4，这时再去 getAttribute 得到的还是"3"

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