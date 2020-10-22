## css优先级、权重(粉笔公考)
### 选择器的优先级

- 选择器越具体，优先级越高。 #xxx 大于 .yyy

- 同样优先级，写在后面的覆盖前面的。

- color: red !important; 优先级最高。

### CSS权重及其引入方式
- !important 优先级最高，但也会被权重高的 important 所覆盖
- 行内样式总会覆盖外部样式表的任何样式(除了!important)
- 单独使用一个选择器的时候，不能跨等级使 css 规则生效
- 如果两个**权重不同的选择器**作用在同一元素上，**权重值高的 css 规则生效**
- 如果两个**相同权重的选择器**作用在同一元素上：**以后面出现的选择器为最后规则**
- 权重相同时，与元素距离近的选择器生效

一句话总结： **!important > 行内样式 > ID 选择器 > (类选择器 | 属性选择器 | 伪类选择器 ) > 元素选择器 >`*`**

## CSS盒子模型及其理解

> 基本概念：content、padding、margin。
>
> 标准盒模型、IE盒模型的区别
>
> CSS如何设置这两种模型（即：如何设置某个盒子为其中一个模型）？
>
> JS如何设置、获取盒模型对应的宽和高？
>
> 盒模型的边距重叠问题（margin塌陷/margin重叠）？
>
> BFC（边距重叠解决方案）或IFC

![截屏2020-09-0123.56.41](https://tva1.sinaimg.cn/large/007S8ZIlly1giblbqcxlxj31au0tw44a.jpg)

盒子模型，英文即Box Model。无论是div、span、还是a都是盒子。

### 盒子中的区域

一个盒子中主要的属性就5个：`width、height、padding、border、margin`。

- width和height：**内容**的宽度、高度（不是盒子的宽度、高度）。
- padding：内边距。
- border：边框。
- margin：外边距。

### 标准盒模型和IE盒模型

> 我们目前所学习的知识中，以标准盒子模型为准。

标准盒子模型：

![](http://img.smyhvae.com/2015-10-03-css-27.jpg)

IE盒子模型：

![](http://img.smyhvae.com/2015-10-03-css-30.jpg)

上图显示：

在 CSS 盒子模型 (Box Model) 规定了元素处理元素的几种方式：



CSS盒模型和IE盒模型的区别：

- 在 <font color="#0000FF">**标准盒子模型**</font>中，<font color="#0000FF">**width 和 height 指的是内容区域**</font>的宽度和高度。增加内边距、边框和外边距不会影响内容区域的尺寸，但是会增加元素框的总尺寸。

- <font color="#0000FF">**IE盒子模型**</font>中，<font color="#0000FF">**width 和height 指的是内容区域+border+padding**</font>的宽度和高度。

### CSS设置这两种模型

代码如下：

```css
/* 设置当前盒子为 标准盒模型（默认） */
box-sizing: content-box;

/* 设置当前盒子为 IE盒模型 */
box-sizing: border-box;
```

备注：盒子默认为标准盒模型。

### JS如何设置、获取盒模型对应的宽和高

#### 方式一：通过DOM节点的 style 样式获取

```css
	element.style.width/height;
```

缺点：通过这种方式，只能获取**行内样式**，不能获取`内嵌`的样式和`外链`的样式。

这种方式有局限性，但应该了解。

#### 方式二（通用型）

```css
    window.getComputedStyle(element).width/height;
```

方式二能兼容 Chrome、火狐。是通用型方式。

#### 方式三（IE独有的）

```css
	element.currentStyle.width/height;
```

和方式二相同，但这种方式只有IE独有。获取到的即时运行完之后的宽高（三种css样式都可以获取）。

#### 方式四

```css
	element.getBoundingClientRect().width/height;
```

此 api 的作用是：获取一个元素的绝对位置。绝对位置是视窗 viewport 左上角的绝对位置。

此 api 可以拿到四个属性：left、top、width、height。

### margin塌陷/margin重叠

**标准文档流中，竖直方向的margin不叠加，只取较大的值作为margin**(水平方向的margin是可以叠加的，即水平方向没有塌陷现象)。

> 如果不在标准流，比如盒子都浮动了，那么两个盒子之间是没有margin重叠的现象的。

**margin这个属性，本质上描述的是兄弟和兄弟之间的距离； 最好不要用这个marign表达父子之间的距离。**如果要表达父子之间的距离，我们一定要善于使用父亲的padding，而不是儿子的margin。

### BFC（边距重叠解决方案）

BFC（Block Formatting Context）：**块级格式化上下文**。你可以把它理解成一个独立的区域。另外还有个概念叫IFC。不过，BFC问得更多。

#### BFC 的原理/BFC的布局规则【非常重要】

BFC 的原理，其实也就是 BFC 的渲染规则。包括：

（1）BFC **内部的**子元素，在垂直方向，**边距会发生重叠**。

（2）BFC在页面中是独立的容器，外面的元素不会影响里面的元素，反之亦然。（稍后看`举例1`）

（3）**BFC区域不与旁边的`float box`区域重叠**。（可以用来清除浮动带来的影响）。（稍后看`举例2`）

（4）计算BFC的高度时，浮动的子元素也参与计算。（稍后看`举例3`）

#### 如何生成BFC

有以下几种方法：

- 方法1：overflow: 不为visible，可以让属性是 hidden、auto。【最常用】
- 方法2：浮动中：float的属性值不为none。意思是，只要设置了浮动，当前元素就创建了BFC。
- 方法3：定位中：只要position的值不是 static或者是relative即可，可以是`absolute`或`fixed`，也就生成了一个BFC。
- 方法4：display为inline-block, table-cell, table-caption, flex, inline-flex

#### BFC 是什么


overflow:hidden ：取消父子 margin 合并。 （另一种推荐做法：`padding-top: 0.1px;`）


#### 如何清除浮动

（1）overflow: hidden

（2）.clearfix 清除浮动写在父元素身上

```css
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}

/* 兼容 IE */
.clearfix {
  zoom: 1;
}
```

## 定位方式及其区别（文档流）
### css定位
css的定位（position）属性有三种，相对定位（relative）、分别是绝对定位（absolute）、固定定位（fixed）。
   >
   > ###### 1、相对定位
   >
   > 相对于自己原来的位置，不脱离标准文档流，相当于它的影子飘出去了。它包括四个定位值，如下：
   >
   > ```css
   > position: relative;
   > top: 50px; // 盒子下移
   > left: 50px; // 盒子右移
   > right: 50px; // 盒子左移
   > bottom: 50px; // 盒子上移
   > // 若值为负数，则表示相反的方向
   > ```
   >
   > 用途：1、可以微调元素；2、可以做绝对定位的参考。
   >
   > ###### 2、绝对定位：
   >
   > 脱离标准文档流，标签不需要区分行内元素、块级元素了，不需要`display:block`就可以设置宽高。
   >
   > ```css
   > position: absolute;
   > top: 50px; // 盒子下移
   > left: 50px; // 盒子右移
   > right: 50px; // 盒子左移
   > bottom: 50px; // 盒子上移
   > // 若值为负数，则表示相反的方向
   > ```
   >
   > 参考点：
   >
   > * 没有包含定位的父辈元素：
   >   * 使用top描述，参考点是页面的左上角，不是浏览器的左上角。
   >   * 使用bottom，参考点是浏览器的首屏窗口尺寸。
   >
   > * 父辈元素包含定位：
   >   * 以最近的已经定位的祖先元素为参考点。
   >   * 不一定是相对定位，任何定位都可以作为儿子的参考点。
   >   * 绝对定位的子元素，无视参考的那个元素的padding。
   >
   > 应用：适合用来做“压盖”效果
   >
   > ###### 3、固定定位：
   >
   > 相对于浏览器窗口进行定位，无论页面如何滚动，这个盒子显示的位置不变。（IE6不兼容）。
   >
   > 用途：网页右下角的返回顶部；固定的顶部导航栏。
   >
   > 在定位问题中，还会存在一个`z-index`属性，可以控制层级数，数值大的压住数值小的。

   > CSS绝对定位absolute详解：https://www.jianshu.com/p/a3da5e27d22b
###  场景题
比如一个场景、child(absolue)、parent（absolute）、grandparent(relative)，子元素应该根据哪个定位？
> 子元素应该根据最近的定位，也就是parent。

## margin塌陷及合并问题

## css属性覆盖问题
> 两个页面的css名称一样，防止一个页面的css属性把另一个同名页面的css属性覆盖了怎么解决了
###### 1、css的scoped属性

vue 为了防止 css 污染，当组件的 `<style>` 标签有 `scoped` 属性时，它的 css 只作用于当前组件中的元素。实现原理很简单，给当前组件中的每个标签都加上唯一的自定义属性：`data-v-唯一的属性`，然后 css 选择器都加上属性选择器`.article-title[data-v-唯一的属性]`，这样这个 css 只会匹配到当前页面的这个元素。

> **注意**：每个组件的最外层的标签会带上父组件的`data-v-`属性，也就是这个标签会被父组件的样式匹配到，所以父组件尽量不要使用标签选择器，这个标签不要使用父组件中的 id 或者 class。

在父组件想修改子组件的css（修改elementUI组件的样式），我们可以借助深度作用选择器 `>>>`

```css
div >>> .el-input{
    width: 100px;
}
/* sass/less的话可能无法识别，这时候需要使用 /deep/ 选择器。 */
div /deep/ .el-input{
    width: 100px;
}
```

如果不用scope怎么解决？

面试官提到了  /deep/ .parent {}，还提到可以通过一个页面

漏了一个(两个style，scoped)

## CSS 阴影属性及具体参数

> 总结：css阴影属性名称是box-shadow，相关参数包括x轴偏移量（正值阴影位于元素右方）、Y轴偏移量（正值阴影位于元素下方）、模糊半径、扩散半径以及颜色，默认阴影是在框外，可以通过设置inset关键字设置阴影在框内。也可以对同一元素添加多个阴影效果，通过逗号将每个阴影规则分隔开。全局关键字包括inherit、initial、unset。

`box-shadow` 属性用于在元素的框架上添加阴影效果。你可以在同一个元素上设置多个阴影效果，并用逗号将他们分隔开。**该属性可设置的值包括阴影的X轴偏移量、Y轴偏移量、模糊半径、扩散半径和颜色。**

```css
/* x偏移量 | y偏移量 | 阴影颜色 */
box-shadow: 60px -16px teal;

/* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影颜色 */
box-shadow: 10px 5px 5px black;

/* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影扩散半径 | 阴影颜色 */
box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.2);

/* 插页(阴影向内) | x偏移量 | y偏移量 | 阴影颜色 */
box-shadow: inset 5em 1em gold;

/* 任意数量的阴影，以逗号分隔 */
box-shadow: 3px 3px red, -1em 0 0.4em olive;

/* 全局关键字 */
box-shadow: inherit;
box-shadow: initial;
box-shadow: unset;
```

向元素添加单个 box-shadow 效果时使用以下规则：

- 当给出两个、三个或四个值时。
  - 如果只给出两个值, 那么这两个值将会被当作 `<offset-x><offset-y>` 来解释。( `<offset-x>` 设置水平偏移量，**正值阴影则位于元素右边**，负值阴影则位于元素左边。 `<offset-y>` 设置垂直偏移量，**正值阴影则位于元素下方**，负值阴影则位于元素上方。)
  - 如果给出了第三个值, 那么第三个值将会被当作`<blur-radius>`解释。(值越大，模糊面积越大，阴影就越大越淡。 不能为负值。默认为0，此时阴影边缘锐利。)
  - 如果给出了第四个值, 那么第四个值将会被当作`<spread-radius>`来解释。(取正值时，阴影扩大；取负值时，阴影收缩。默认为0，此时阴影与元素同样大。)
- 可选，`inset`关键字。(使用 `inset` 关键字会使得阴影落在盒子内部，这样看起来就像是内容被压低了。 此时阴影会在边框之内 (即使是透明边框）、背景之上、内容之下。)
- 可选，`<color>`值。

若要对同一个元素添加多个阴影效果，请使用逗号将每个阴影规则分隔开。

###### 示例：

1、包括三种shadows，内置的阴影, 常规的下沉阴影, 和一个2个像素宽度的border式的阴影 (可以用 [`outline`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/outline) 来替代第三种)。

```css
<blockquote><q>You may shoot me with your words,<br/>
You may cut me with your eyes,<br/>
You may kill me with your hatefulness,<br/>
But still, like air, I'll rise.</q>
<p>&mdash; Maya Angelou</p>
</blockquote>

blockquote {
  padding: 20px;
  box-shadow: 
       inset 0 -3em 3em rgba(0,0,0,0.1), 
             0 0  0 2px rgb(255,255,255),
             0.3em 0.3em 1em rgba(0,0,0,0.3);
}
```

![image-20200912095018399](https://tva1.sinaimg.cn/large/007S8ZIlly1ginmnho1ghj30ys0cyjsx.jpg)

2、当 `x-offset`, `y-offset`, 和 `blur` 都是0, 盒阴影将是一个四边都是一样长度的带有颜色的`outline`

```css
<div><p>Hello World</p></div>
p {
  box-shadow: 0 0 0 2em #F4AAB9,
              0 0 0 4em #66CCFF;
  margin: 4em;
  padding:1em;
}
```

当设置了多个阴影时，阴影绘制由最后一个开始， 故第一个设置的阴影将覆盖在后设置的阴影之上. 当`border-radius设置为0时（也是其默认值）`, 阴影的四角将没有弧度. 当我们设置了`border-radius`为其他不为0的值时，阴影的四角也随之形成弧度。

我们通常在元素上增加一个大小为最大阴影宽度的`margin`值以保证阴影不会覆盖到相邻的元素或者覆盖到元素的`border`上. `box-shadow`属性不会影响到盒模型的构成。

## 浮动模型及清除浮动的方法
css定位分为三种：标准流、浮动、绝对定位。 

标准流：默认状态下，元素自动从左往右，从上往下的排列。

### 浮动的基础知识 

1、会使元素向左或向右移动，只能左右，不能上下。 

2、浮动元素碰到包含框或另一个浮动框，浮动停止。 

3、浮动元素之后的元素将围绕它，之前的不受影响。

4、浮动元素会脱离标准流。 

5、元素浮动后具备inline-block属性。
### 浮动的基本语法

```css
float：none；  没有浮动；
float：left；  左浮动；
float：right； 右浮动；
float：inherit；继承父元素的浮动；
```
### 浮动注意的地方

使用浮动的时候，**子元素的浮动会导致父元素发生塌陷**，因为子元素进行了浮动，脱离了标准流，使得父元素无法检测到子元素，导致父元素塌陷、没有高度；同理，因为浮动元素脱离了标准流，使得**临近元素无法感知到浮动元素，使得普通元素占据浮动元素的位置，发生异位**。 

使用clear属性清除浮动，clear 属性可选参数值为left 、 right 、 both、 none、 inherit。 **设置了float的元素会影响其他相邻元素，需要使用clear清除浮动，clear只会影响自身**，不会对其他相邻元素造成影响。
### 清除浮动的三种方法

1、在父元素内最后一行增加一个元素标签，比如 div 设置其class,增加样式`clear:both`。 

```css
<style>
.child {
  float: left;
}
/* 清除浮动 */
.clearfix {
  clear: both;
}
</style>
<body>
  <div class="parent">
    <div class="child"></div>
		/* 增加一行，设置样式清除浮动 */
		<div class="clearfix"></div>
  </div>
</body>
```

> 缺点：产生大量的空元素，浪费资源。

2、BFC清除浮动，在父元素class里面添加样式overflow:hidden;zoom:1;(兼容低版本)。 

```html
<style>
  .parent {
    overflow: hidden;
    zoom: 1; // 兼容低版本
  }
	.child {
  	float: left;
	}
</style>
<body>
  <div class="parent">
    <div class="child"></div>
  </div>
</body>
```

3、在父元素class添加伪元素（最推荐）

```css
<style>
.parent::after{
  content:'';
  display:block;
  clear:both;
}
/* 兼容 IE */
.parent {
  zoom: 1;
}
</style>
<body>
  <div class="parent">
    <div class="child"></div>
  </div>
</body>
```

> https://juejin.im/post/6844903878861783048

## BFC
### 什么是BFC

BFC（Block Formatting Context）：**块级格式化上下文**。是Web页面可视化渲染CSS的一部分, 是布局过程中生成块级盒子的区域。也是浮动元素与其他元素的交互限定区域。

> 可以把它理解成一个独立的区域。区域内的元素在布局上不会影响外面的元素。

### 如何生成BFC

1. **根元素或包含根元素的元素** 
2. **浮动元素（元素的 float 不是 none）** 
3. **绝对定位元素（元素的 position 为 absolute 或 fixed）** 
4. **行内块元素（元素的 display 为 inline-block）**
5. **overflow 值不为 visible 的块元素**
6. **弹性元素（display为 flex 或 inline-flex元素的直接子元素）** 
7. 表格单元格（元素的 display为 table-cell，HTML表格单元格默认为该值） 
8. 表格标题（元素的 display 为 table-caption，HTML表格标题默认为该值）
9. 匿名表格单元格元素（元素的 display为 table、table-row、 table-row-group、table-header-group、table-footer-group（分别是HTML table、row、tbody、thead、tfoot的默认属性）或 inline-table） 
10. display 值为 flow-root 的元素 
11. contain 值为 layout、content或 strict 的元素 
12. 网格元素（display为 grid 或 inline-grid 元素的直接子元素） 
13. 多列容器（元素的 column-count 或 column-width 不为 auto，包括 column-count 为 1） 
14. column-span 为 all 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中（标准变更，Chrome bug）。

### BFC常见的应用

###### 1、解决普通文档流块元素的外边距折叠问题

```html
<style>
  div {
    width: 100px;
    height: 100px;
  }   
  .child1 {
    margin: 10px 0;
    background-color: blueviolet;
  }
  .child2 {
    margin: 20px 0;
    background-color: pink;
  }
</style>
<body>
  <div class="parent">
    <div class="child1"></div>
    <div class="child2"></div>
  </div>
</body>
```
![](https://tva1.sinaimg.cn/large/007S8ZIlly1gjdfwuwl15j32800rutdz.jpg)

从图中可以看出，两个块元素外边距为20px，出现边距重叠问题。可以通过将两个块元素置于不同的BFC中进行隔离解决。

```html
<style>
  .parent div {
    width: 100px;
    height: 100px;
  }
  /* BFC */
  .parent {
    overflow: hidden;
  } 
  .child1 {
    margin: 10px 0;
    background-color: blueviolet;
  }
  .child2 {
    margin: 20px 0;
    background-color: pink;
  }
</style>
<body>
  <!-- 使用BFC解决问题 -->
    <div class="parent">
        <div class="child1"></div>
    </div>
    <div class="parent">
        <div class="child2"></div>
    </div>
</body>
```

![image-20201004182953246](https://tva1.sinaimg.cn/large/007S8ZIlly1gjdhauiw8nj32800riag5.jpg)



###### 2、BFC清除浮动

子元素设置浮动，脱离文档流，导致父元素坍塌，没有高度，可以通过设置BFC，在父元素添加`overflow: hidden`清除浮动。

**清除浮动前**

```css
<style>
  .parent {
    border: 1px solid red;
  }
	.child {
  	float: left;
    width: 100px;
    height: 100px;
    background-color: pink;
	}
</style>
<body>
  <div class="parent">
    <div class="child"></div>
  </div>
</body>
```

![清除浮动前](https://tva1.sinaimg.cn/large/007S8ZIlly1gjdhygqcrcj30tq06uq2w.jpg)

**清除浮动后**

```html
<style>
  .parent {
    border: 1px solid red;
    /* 清除浮动 */
    overflow: hidden;
    zoom: 1; /* 兼容低版本 */
  }
	.child {
  	float: left;
    width: 100px;
    height: 100px;
    background-color: pink;
	}
</style>
<body>
  <div class="parent">
    <div class="child"></div>
  </div>
</body>
```

![清除浮动后](https://tva1.sinaimg.cn/large/007S8ZIlly1gjdhwifdnoj30u0082q2x.jpg)

###### 3、解决普通文档流元素被浮动元素覆盖问题

在父元素中有两个子元素，其中一个设置浮动，一个没有设置，会导致没有设置浮动的元素被浮动元素覆盖的问题。

```css
<style>
.child1 {
  float: left;
  width: 100px;
  height: 100px;
  background-color: pink;
}

.child2 {
  width: 200px;
  height: 200px;
  background-color: blueviolet;
}
</style>
<body>
<div class="parent">
<div class="child1">左侧浮动元素</div>
<div class="child2">没有设置浮动元素，没有触发BFC</div>
</div>
</body>
```

![image-20201004190527423](https://tva1.sinaimg.cn/large/007S8ZIlly1gjdibuyrhtj30tg0bwdgh.jpg)

触发BFC解决覆盖问题。

```css
<style>
.child1 {
  float: left;
  width: 100px;
  height: 100px;
  background-color: pink;
}

.child2 {
  width: 200px;
  height: 200px;
  background-color: blueviolet;
  /* 触发BFC */
  overflow: hidden;
}
</style>
<body>
<div class="parent">
<div class="child1">左侧浮动元素</div>
<div class="child2">没有设置浮动元素，没有触发BFC</div>
</div>
</body>
```

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gjdiep1u51j30tm0bkgm7.jpg)

## 伪类和伪元素的区别

概念上的区别：

- 伪类表示一种状态。

- 伪元素是真的有元素。比如 `::after` 是真的有元素，可以在页面上显示内容。

使用上的区别：

- 伪类：使用单冒号

- 伪元素：使用双冒号


## CSS定位属性

## css的flex布局

> * flex布局是弹性布局，任何一个容器都可以指定为flex布局，包括行内元素，通过inline-flex属性进行设置。
>
> * flex布局分为主轴和交叉轴，常见的容器的属性主要包括`flex-direction、flex-wrap、justify-content、align-items`等，
>   * `flex-direction`属性是决定主轴的方向row和column，
>   * `flex-wrap`属性定义如果一条轴线排不下，如何换行。常用的是wrap：换行，第一行在上方。默认不换行。
>   * `justify-content`属性：定义了项目在主轴上的对齐方式，常用center。
>   * `align-items`属性：定义项目在交叉轴上如何对齐,常用center。
>
> * 对于flex布局元素的子元素而言，常用的属性包括flex-grow、flex-shrink、flex-basis；
>   * `flex-grow`属性：定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。
>   * `flex-shrink`属性：定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
>   * `flex-basis`属性：定义了在分配多余空间之前，项目占据的主轴空间。默认值为`auto`，即项目的本来大小。
>   * flex属性主要是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。
>
> 当flex为none时,等同于flex: 0 0 auto;
>
> 当flex为auto时，等同于flex: 1 1 auto;
>
> 当flex取值为一个数字，则该数字是设置的flex-grow值，其它两个属性用初始值，如flex:1时，等同于flex: 1 1 0%;
>
> 当flex取值为2个数字时，相当于设置的flex-grow和flex-shrink值，flex-basis取值为初始值，如flex:1 0时，等同于flex: 1 0 0%

#### flex布局

* 弹性布局，为盒子模型提供了最大的灵活性。
* **采用 Flex 布局的元素，称为 Flex 容器**，简称"容器"。
* 它的**所有子元素自动成为容器成员，称为 Flex 项目**，简称"项目"。

* 任何一个容器都可以指定为 Flex 布局。行内元素也可以使用 Flex 布局。Webkit 内核的浏览器，必须加上`-webkit`前缀。

> 设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效。

```js
.box{
  display: flex;
  display: inline-flex;
  display: -webkit-flex; /* Safari */
}
```

#### 容器的属性

flex布局分为两根轴线，水平的主轴和垂直的交叉轴。常见的属性包括：

```css
flex-direction
flex-wrap
flex-flow
justify-content
align-items
align-content
```

* 1、 flex-direction属性：**决定主轴的方向**

```css
.box {
  flex-direction: row | row-reverse | column | column-reverse;
}
row（默认值）：主轴为水平方向，起点在左端。
row-reverse：主轴为水平方向，起点在右端。
column：主轴为垂直方向，起点在上沿。
column-reverse：主轴为垂直方向，起点在下沿。
```

* 2、flex-wrap属性：**默认情况下，项目都排在一条线（又称"轴线"）上**。`flex-wrap`属性定义，如果一条轴线排不下，如何换行。

```js
.box{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
nowrap（默认）：不换行。
wrap：换行，第一行在上方。
wrap-reverse：换行，第一行在下方。
```

* 3、flex-flow属性：是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap`。

* 4、justify-content属性：定义了项目在主轴上的对齐方式。

  ```css
  .box {
    justify-content: flex-start | flex-end | center | space-between | space-around;
  }
  flex-start（默认值）：左对齐
  flex-end：右对齐
  center： 居中
  space-between：两端对齐，项目之间的间隔都相等。
  space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。
  ```

* 5、align-items属性：定义项目在交叉轴上如何对齐。

  ```css
  .box {
    align-items: flex-start | flex-end | center | baseline | stretch;
  }
  flex-start：交叉轴的起点对齐。
  flex-end：交叉轴的终点对齐。
  center：交叉轴的中点对齐。
  baseline: 项目的第一行文字的基线对齐。
  stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。
  ```

* 6、align-content属性：定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

  ```css
  .box {
    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
  }
  flex-start：与交叉轴的起点对齐。
  flex-end：与交叉轴的终点对齐。
  center：与交叉轴的中点对齐。
  space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
  space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
  stretch（默认值）：轴线占满整个交叉轴。
  ```

#### 项目的属性

```css
order
flex-grow
flex-shrink
flex-basis
flex
align-self
```

* 1、order属性：定义项目的排列顺序。数值越小，排列越靠前，默认为0。

* 2、flex-grow属性：定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。

  > ```css
  > .item {
  > flex-grow: <number>; /* default 0 */
  > }
  > ```

  如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

* 3、flex-shrink属性：定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

  ```css
  .item {
    flex-shrink: <number>; /* default 1 */
  }
  ```

  如果所有项目的`flex-shrink`属性都为1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。

* 4、flex-basis属性：定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。

  ```css
  .item {
    flex-basis: <length> | auto; /* default auto */
  }
  ```

  它可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间。

* flex属性：是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

  ```css
  .item {
    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
  }
  ```

  该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

* align-self属性：`align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

  ```css
  .item {
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
  }
  ```

  > 参考链接：[阮一峰](http://www.ruanyifeng.com/)：http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html


## display及相关属性

## css常用的单位
- `px`：代表像素，是一个**相对长度单位**，1px占据显示设备上的一个像素点。可以用来指定字体大小，元素的宽度、高度、边框、内边距，外边距的大小等等。
  - in - 表示英寸，是一个物理单位，在CSS中被直接映射成为px; 转换的方法是 1in = 96px
  - cm - 表示厘米，在生活中常用的单位，同样被映射为 px; 转换方法为 1cm = 37.8px
  - mm - 表示毫米，与cm类似，转换方法为 1mm = 0.1cm = 3.78px
- `%`： 相对长度单位，相对于父元素的尺寸的取值，实际使用中，如果父元素是一个非稳定的取值，可能会导致父元素被撑开，而实际值取决于其祖先元素中最近的一个拥有稳定取值的元素。整数取值，并不适用于解决自适应问题。
- `em`：是一个相对的长度单位，会根据对象内的font-size大小成倍关系。
  - 优点：能够适应屏幕发生的变化。
  - 缺点：进行设置的时候需要知道父元素的font-size大小。
- `rem`：跟`em`类似，不同之处在于`rem`参考的对象是根元素`html`，因此rem更清晰不易出错。
- `vw`和 `vh`：相对长度单位，`vw`是首屏的宽度，`vh`是首屏的高度。取值范围是[0,100]。适用于依据屏幕宽或高进行的排版，适用于自适应。
- `vmin` 和 `vmax`：相对长度单位，vmin`是比较首屏的宽高，选取其中最小的作为参考等分100份；`vmax`是比较首屏的宽高，选取其中最大的作为参考等分100份。取值范围是[0,100]。
- `pt` （点）：绝对长度单位，多用于字体尺寸，1px = 0.75pt。

## CSS实现隐藏页面的方式 
隐藏盒子，有以下几种方式：

（1）方式一：

```
overflow：hidden;   //隐藏盒子超出的部分
```

（2）**方式二**：

```
display: none;	  隐藏盒子，而且不占位置(用的最多)
```

比如，点击`X`，关闭京东首页上方的广告栏。

（3）方式三：

```
visibility: hidden;   //隐藏盒子，占位置。
visibility: visible;   //让盒子重新显示

```

（4）方式四：

```
opacity: 0;       //设置盒子的透明度（不建议，因为内容也会半透明），占位置
```


（4）方式五：

```
Position/top/left/...-999px   //把盒子移得远远的，占位置。
```

（5）方式六：

```
margin-left: 1000px;
```
## property 和 attribute 的区别

例如一个 input 标签 `<input value="3" />` 它的 attribute 是 3 但如果使用`input.value = 4` 或 直接修改值为 4，这时再去 getAttribute 得到的还是"3"