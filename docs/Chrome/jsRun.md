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

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glgx5ctg43j30wo05cwgh.jpg" alt="JavaScript的执行流程图" style="zoom:50%;" />

#### 1. 编译阶段

输入一段代码，经过编译后，会生成两部分内容：**执行上下文（Execution context）和可执行代码。**

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glgx843m2ej30uc0gan6e.jpg" alt="JavaScript执行流程细化图" style="zoom:50%;" />

**执行上下文是 JavaScript 执行一段代码时的运行环境**，比如调用一个函数，就会进入这个函数的执行上下文，确定该函数在执行期间用到的诸如 this、变量、对象以及函数等。

**在执行上下文中存在一个变量环境的对象（Viriable Environment）**，**该对象中保存了变量提升的内容**，比如上面代码中的变量myname 和函数 showName，都保存在该对象中。

#### 2. 执行阶段

有了执行上下文和可执行代码后，JavaScript 引擎开始执行“可执行代码”，按照顺序一行一行地执行。

!> 一段代码如果定义了两个相同名字的函数，那么最终生效的是最后一个函数。

### 总结

* JavaScript 代码执行过程中，需要先做**变量提升**，而之所以需要实现变量提升，是因为JavaScript **代码在执行之前需要先编译**。
* **在编译阶段，变量和函数会被存放到变量环境中**，变量的默认值会被设置为undefined；在代码执行阶段，JavaScript 引擎会从变量环境中去查找自定义的变量和函数。
* 如果在编译阶段，**存在两个相同的函数，那么最终存放在变量环境中的是最后定义的那个**，这是因为后定义的会覆盖掉之前定义的。