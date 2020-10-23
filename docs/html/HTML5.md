## HTML5的存储方案

> 1、**`window.sessionStorage` 会话存储：**
>
> - 保存在内存中。
>
> - **生命周期**为关闭浏览器窗口。也就是说，当窗口关闭时数据销毁。
>
> - 在同一个窗口下数据可以共享。
>
> 2、**`window.localStorage` 本地存储**：
>
> - 有可能保存在浏览器内存里，有可能在硬盘里。
>
> - 永久生效，除非手动删除（比如清理垃圾的时候）。
>
> - 可以多窗口共享。

常见的浏览器端数据存储方案有：

* ##### Cookie

  Cookie的又称是HTTP Cookie，最初是在客户端用于存储会话信息，Cookie通常用于存储一些通用的数据，比如用户的登陆状态，首选项等。

  优点：相比于其他的数据存储方式，Cookie的兼容性非常的好，兼容现在市面上所有的主流浏览器。

  缺点：**存储量小**。不同的浏览器存储量不同，但基本上都在4k左右。**影响性能**。Cookie会由浏览器作为请求头发送，当Cookie存储信息过多时，会影响特定域的资源获取的效率，增加文档传输的负载。**只能储存字符串**。**安全问题**。存储在Cookie的任何数据可以被他人访问，因此不能在Cookie中储存重要的信息。

* ##### web存储 (localStorage和seesionStorage)

  localStorage能存储字符串，也可以存储字符串化的JSON数据，相比于cookie，它**提供了简单明了的API来进行操作；更加安全；可储存的数据量更大**。通过**localStorage存储的数据是永久性的**，除非我们使用removeItem来删除或者用户通过设置浏览器配置来删除，负责数据会一直保留在用户的电脑上，永不过期。localStorage的作用域限定在文档源级别的，**同源的文档间会共享localStorage的数据**，他们可以互相读取对方的数据，甚至有时会覆盖对方的数据。

  sessionStorage 与 localStorage 相似，不同之处在于 localStorage里面存储的数据没有过期时间设置，而**Session Storage只存储当前会话页的数据，且只有当用户关闭当前会话页或浏览器时，数据才会被清除。**

* ##### IndexedDB

  它是由HTML5所提供的一种本地存储，**用于在浏览器中储存较大数据结构的 Web API，并提供索引功能以实现高性能查找。**它一般用于保存大量用户数据并要求数据之间有搜索需要的场景，当网络断开时，用户就可以做一些离线的操作。**indexedDB使用同源原则，它不能被任何其他源访问。不适合存储敏感数据。**

> https://juejin.im/post/6844903801745309710

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

## HTML5 的 form 关闭自动补全功能

给不想要提示的 form 或某个 input 设置为 autocomplete=off。

## HTML5 的离线储存

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

### 浏览器对 HTML5 的离线储存资源的管理和加载

- 在线的情况下，浏览器发现 html 头部有 manifest 属性，它会请求 manifest 文件，如果是第一次访问 app，那么浏览器就会根据 manifest 文件的内容下载相应的资源并且进行离线存储。如果已经访问过 app 并且资源已经离线存储了，那么浏览器就会使用离线的资源加载页面，然后浏览器会对比新的 manifest 文件与旧的 manifest 文件，如果文件没有发生改变，就不做任何操作，如果文件改变了，那么就会重新下载文件中的资源并进行离线存储。
- 离线的情况下，浏览器就直接使用离线存储的资源。

在离线状态时，操作 window.applicationCache 进行需求实现。

参考链接：[HTML5 离线缓存-manifest 简介](https://yanhaijing.com/html/2014/12/28/html5-manifest/)
