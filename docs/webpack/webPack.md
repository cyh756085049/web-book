## Webpack学习

Webpack是什么？

是一种前端资源构建工具，一个静态模块打包器（model bundler）。在Webpack看来，前端所有的资源文件（js/json/css/less/img...）都会作为模块处理。它将根据模块的依赖关系进行静态分析，打包生成对应的静态资源（bundle）。

```js
npm init
npm i jquery
npm i webpack webpack-cli -g
npm i webpack webpack-cli -D
```

![image-20201002095156309](https://tva1.sinaimg.cn/large/007S8ZIlly1gjar3b4kanj31780u07fu.jpg)

首先入口文件为index.js，在文件中通过js、less、css等引入文件进行编译成chunk块，然后将less转化成css等进行打包，最后bundle

## Webpack五个核心概念

- entry：入口。webpack是基于模块的，使用webpack首先需要指定模块解析入口(entry)，webpack从入口开始根据模块间依赖关系递归解析和处理所有资源文件。
- output：输出。源代码经过webpack处理之后的最终产物。
- loader：模块转换器。本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，对其他类型的资源进行转译的预处理工作。
- plugin：扩展插件。基于事件流框架 `Tapable`，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。
- module：模块。除了js范畴内的es module、commonJs、AMD等，css @import、url(...)、图片、字体等在webpack中都被视为模块。


## package.json、package-lock.json区别

package.json是通过npm init创建时生成的，package.json文件中会记录项目中所需要的模块。记录的只是每个模块的基本信息。模块名称和大版本信息。

在使用npm install的时候会自动生成一个package-lock.json的文件，package-lock.json文件则会记录每个模块的详细信息，如模块的具体版本号和各个模块所依赖的子模块的信息。

npm install的过程大致就是从package.json中读取所有的依赖信息，然后再与node_modules中已经安装的依赖进行对比，如果没有则通过package-lock.json获取相应版本号下载安装.如果已经存在则会通过package-lock.json检查更新。

进行更新的原则就是其范围是在package.json中对应安装包版本所容纳的版本。`^`就是指兼容该版本以后的小版本而不更新大版本，如"vue":"`^`2.5.2"也就是指范围应该在>=2.5.2和<3.0.0之间(tips:网上大多数帖子说的比较笼统，而参考其中的一篇帖子大致就是，^会忽视版本号开头为0的数字，也就是说"axios":"0.19.0"的范围应该在>=0.19.0和<0.20.0之间)

> 参考：阿豪同学：https://juejin.im/post/6844903907290775565

## 前端工程化的流程（架构选型、业务开发、测试、打包构建、部署上线、项目监控）

## Webpack基本概念与配置

## loader与plugin原理与实现

## Webpack的模块热替换及实现

## Webpack的优化问题

##  SPA及其优缺点

## SSR实现及优缺点

## 设计模式（工厂模式、单例模式、原型模式、***模式、适配器模式、观察者模式等...）



## 移动端的网页，不同设备上的适配，有什么方案？

### 1.media queries(媒体查询) 

主要就是运用css来判断设备宽度在不同的区间运用不同的样式，个性化定制会比较高 。

### 2.rem

rem（font size of the root element）是指相对于根元素的字体大小的单位。简单的说它就是一个相对单位，rem计算的规则是依赖根元素。**原理是先按定高宽设计出来页面，然后转换为rem单位，配合js查询屏幕大小来改变html的font-size** 。

> 实现代码参考：https://segmentfault.com/a/1190000012225828?utm_source=tag-newest

### 3.flex或者百分比布局

Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。 一般移动端浏览器对flex支持都比较好 所以很多布局思路会使用弹性盒子布局 可以结合百分比布局来实现想要的效果

### 4.viewport

viewprot 指的是移动设备浏览器中放置页面的一个虚拟的窗口，该窗口可大于或小于移动设备的可视区域。当我们将一个pc端的网页放到移动端的时候，移动端浏览器会将pc端的网页按照一定的比例完整的显示出来，**这是因为移动端的浏览器默认的会将网页渲染在一个比例比较大的viewport中排版（ios默认的是980px，Android4.0以上为980px），然后通过比例缩放看到整个页面的全貌。**

###### 小结

如果考虑做一个自适应的网站，那么开始的时候就需要把思路规划好，根据设计稿来设置自己的rem，使用媒体查询 flex和百分比布局等可以用一套代码个性化的去定制pc端和移动端样式。

如果需要对现有pc端项目进行移动端改造，那么可以根据项目复杂程度自行选用技术方案，当然 如果对自适应要求不是很高，对于老旧项目改造的时候 可以考虑将项目版心宽度作为自适应的基础宽度，然后去掉viewport设置让浏览器自行缩放，虽然可能效果不如自己个性化定制那么好，但是也是可以作为一种较为简单通用的方案去使用。

> 链接：https://juejin.im/post/6844903988488306701

