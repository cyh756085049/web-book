## 变量提升

> JavaScript代码是按顺序执行的吗？

1. 在执行过程中，若使用了未声明的变量，那么 JavaScript 执行会报错。

2. 在一个变量定义之前使用它，不会出错，但是该变量的值会为 undefined，而不是定义时的值。
3. 3. 在一个函数定义之前使用它，不会出错，且函数能正确执行。

### 变量提升

定义：是指在 JavaScript 代码执行过程中，**JavaScript 引擎把变量的声明部分和函数的声明部分提升到代码开头**。变量被提升后，会给变量设置默认值即`undefined`。

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glgvpm99pcj30v80eq45o.jpg" alt="变量、函数提升示意图" style="zoom:50%;" />

### JavaScript 代码的执行流程

**变量提升实际上是变量和函数声明在代码里的位置是不会改变的，而是在编译阶段被 JavaScript 引擎放入内存中。**一段JavaScript 代码在执行之前需要被 JavaScript 引擎编译，编译完成之后，才会进入执行阶段。

