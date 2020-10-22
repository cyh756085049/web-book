## CSS画三角形
> 给定一个`div`，设置其宽高为0，然后设置边框`border`属性，给一个大小50px，实线类型，透明色，然后给边框的上下左右其中的一个设置颜色，即完成三角形的制作。
```css
<style>
	// 第一种方式
  div {
    width: 0;
    height: 0;
    border: 50px solid transparent;
    border-top-color: red;
    border-bottom: none;
  }
	// 第二种方式
  div {
    width: 0;
    height: 0;
    border-top: 30px solid red;
    border-left: 20px solid transparent;
    border-right: 20px solid transparent;
  }
</style>
<body>
    <div></div>
</body>
```
> 细节参考：http://caibaojian.com/css-border-triangle.html

## 自适应两栏布局

```css
<style>
.left {
  width: 200px;
  height: 200px;
  float: left;
  background-color: blueviolet;
}

.right {
  height: 200px;
  background-color: brown;
  overflow: hidden;
}
</style>
<body>
<div class="container">
<div class="left">浮动元素</div>
<div class="right">自适应元素</div>
</div>
</div>
</body>
```
![自适应两栏布局](https://tva1.sinaimg.cn/large/007S8ZIlly1gjdioder0qj31380bqdg8.jpg)
> https://juejin.im/post/6844903780253696013#comment

## 元素水平垂直居中（方案及比较）

![image-20200912102521366](https://tva1.sinaimg.cn/large/007S8ZIlly1ginnnwrvvzj31aa0f8go3.jpg)

#### 1、一个行内元素（文字、图片等）水平垂直居中

##### 行内元素水平居中

给父容器设置：

```css
text-align: center;
```

##### 行内元素垂直居中

让**文字的行高** 等于 **盒子的高度**，可以让单行文本垂直居中。比如：

```css
.father {
  height: 20px;
  line-height: 20px;
}
```

#### 2、一个块级元素水平垂直居中

##### 块级元素水平居中

```css
margin: auto;
margin: 0 auto; // 等价于 margin: 0 auto 0 auto（上右下左）
```

##### 块级元素的水平垂直居中

* ###### 绝对定位 + `margin`

  先让子元素相对于父元素进行绝对定位，并在左上角居中，然后向上移动子元素高度的一半，向左移动子元素宽度的一半，即达到垂直居中的效果。

  缺点：需要指定子元素的宽高。

```html
<style>
  /* 绝对定位 + margin (需要指定子元素的宽高) */
  .box {
    width: 100%;
    height: 500px;
    background: violet;
    position: relative;
  }

  .div1 {
    width: 300px;
    height: 200px;
    background: wheat;
    position: absolute;
    top: 50%;
    left: 50%;
    margin-top: -100px;
    margin-left: -150px;
  }
</style>
<body>
    <div class="box">
        <div class="div1"></div>
    </div>
</body>
```

* 绝对定位 + `translate`

  在**无需指定子元素宽高度**的情况下，使用`transform`中的`translate()`函数以这个元素自身的宽高度为基准进行横坐标和纵坐标的移动。

```html
<style>
  /* 绝对定位 + translate */
  .box {
    width: 100%;
    height: 500px;
    background: violet;
    position: relative;
  }

  .div1 {
    position: absolute;
    top: 50%;
    left: 50%;
    background: wheat;
    transform: translate(-50%, -50%);
  }
</style>
<body>
    <div class="box">
        <div class="div1">子元素</div>
    </div>
</body>
```

* `flex`布局

  将父容器设置为 flex 布局，给父容器添加属性`justify-content: center`，子元素水平居中；然后添加属性 `align-items: center`，子元素垂直居中。

  缺点：如果父容器中有多个子元素的话，父容器里的所有子元素们都会垂直居中

```html
<style>
  .box {
    display: flex;
    justify-content: center;
    /* 设置子元素在侧轴上的对齐方式，中间对齐 */
    align-items: center;
    height: 500px;
    background: violet;
  }
  .div1 {
    background: wheat;
  }
  .div2 {
    background: yellow;
  }
</style>
<body>
    <div class="box">
        <div class="div1">子元素</div>
      	<div class="div2">子元素2</div>
    </div>
</body>
```

* `flex`布局 + `margin` （推荐）

给父容器设置`flex`布局:`display:flex`，然后给子元素设置`margin:auto`，达到水平居中效果。这种方式可以解决父元素中的某个子元素居中的问题。

```html
<style>
  .box {
    display: flex;
    height: 500px;
    background: violet;
  }
  .div1 {
    margin: auto;
    background: wheat;
  }
  .div2 {
    background: yellow;
  }
</style>
<body>
    <div class="box">
        <div class="div1">子元素</div>
      	<div class="div2">子元素2</div>
    </div>
</body>
```



## CSS浮动时等宽等高


## 三栏布局，中间自适应
> 圣杯布局，双飞翼布局，flex布局