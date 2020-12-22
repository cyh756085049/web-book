## 变量提升

> JavaScript代码是按顺序执行的吗？

1. 在执行过程中，若使用了未声明的变量，那么 JavaScript 执行会报错。

2. 在一个变量定义之前使用它，不会出错，但是该变量的值会为 undefined，而不是定义时的值。
3. 在一个函数定义之前使用它，不会出错，且函数能正确执行。

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

## 调用栈

> 为什么JavaScript代码会出现栈溢出？

在**执行之前就进行编译并创建执行上下文的代码**一般有以下三种情况：

1. 当 JavaScript **执行全局代码**的时候，**会编译全局代码并创建全局执行上下文**，而且在整个页面的生存周期内，全局执行上下文**只有一份**。

2. 当**调用一个函数的时候**，函数体内的代码会被**编译**，并**创建函数执行上下文**，一般情况下，**函数执行结束**之后，创建的**函数执行上下文会被销毁**。
3. 当使用 **eval 函数**的时候，eval 的代码也会被编译，并**创建执行上下文**。

在我们进行JavaScript编码时，有时候会遇到栈溢出的错误，此时就会涉及到调用栈的内容。在JavaScript 中有很多函数，经常会出现在一个函数中调用另外一个函数的情况，**调用栈就是用来管理函数调用关系的一种数据结构。**

### JavaScript的调用栈

JavaScript 引擎正是利用栈的这种结构来管理执行上下文的。**在执行上下文创建好后，JavaScript 引擎会将执行上下文压入栈中**，通常把这种用来管理执行上下文的栈称为执行上下文栈，又称调用栈。

在代码的执行过程中，首先会创建全局上下文，包含定义的变量和创建的函数，并将其压入栈底，全局执行上下文压入到调用栈后，JavaScript 引擎便开始执行全局代码了。当遇到全局变量赋值操作时，将全局变量的undefined修改为对应的值，当调用函数时，首先JavaScript引擎会编译该函数，并为其创建一个函数执行上下文，并将其压入栈中，然后进入函数代码执行阶段，遇到赋值操作，直接赋值，遇到调用函数语句时，再次为其创建函数执行上下文，压入栈进行操作，当此函数返回时，该函数的执行上下文就会从栈顶弹出，并将返回值返回，将其函数执行上下文从栈顶弹出，此时调用栈中只剩下全局上下文了。

#### 1、利用浏览器查看调用栈的信息

打开“开发者工具”，点击“Source”标签，选择 JavaScript 代码的页面，然后在代码行加上断点，并刷新页面。你可以看到执行到 add 函数时，执行流程就暂停了，这时可以通过右边“callstack”来查看当前的调用栈的情况，如下图：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glhxmk1zznj30ug0gu7ax.jpg" alt="函数调用关系" style="zoom:50%;" />

从图中可以看出，右边的“call stack”下面显示出来了函数的调用关系：**栈的最底部是anonymous，也就是全局的函数入口**；中间是 addAll 函数；顶部是 add 函数。这就清晰地反映了函数的调用关系，所以在分析复杂结构代码，或者检查 Bug 时，调用栈都是非常有用的。

除了通过断点来查看调用栈，你还**可以使用 `console.trace() `来输出当前的函数调用关系**，比如在示例代码中的 add 函数里面加上了 console.trace()，你就可以看到控制台输出的结果，如下图：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glhxpsnq9xj30tm0m444u.jpg" alt="" style="zoom:50%;" />

#### 2. 栈溢出（Stack Overflow）

**调用栈是有大小的，当入栈的执行上下文超过一定数目**，JavaScript 引擎就会报错，我们把这种错误叫做**栈溢出**。

递归代码就很容易出现栈溢出的情况，如下代码：

```js
function division(a,b){    
  return division(a,b);
}
console.log(division(1,2));
```

当执行时，就会报错栈溢出，如下图：

![栈溢出](https://tva1.sinaimg.cn/large/0081Kckwly1glhxuj2r87j31a80fadjn.jpg)

因为当 JavaScript 引擎开始执行这段代码时，它首先调用函数 division，并创建执行上下文，压入栈中；然而，**这个函数是递归的，并且没有任何终止条件**，所以它会一直创建新的函数执行上下文，并反复将其压入栈中，但**栈是有容量限制的**，超过最大数量后就会出现栈溢出的错误。

理解了栈溢出原因后，我们就可以使用一些方法来避免或者解决栈溢出的问题，比如把递归调用的形式改造成其他形式，或者使用加入定时器的方法来把当前任务拆分为其他很多小任务。

### 总结

每调用一个函数，JavaScript 引擎会为其创建执行上下文，并把该执行上下文压入调用栈，然后 JavaScript 引擎开始执行函数代码。

如果在一个函数 A 中调用了另外一个函数 B，那么 JavaScript 引擎会为 B 函数创建执行上下文，并将 B 函数的执行上下文压入栈顶。

当前函数执行完毕后，JavaScript 引擎会将该函数的执行上下文弹出栈。

当分配的调用栈空间被占满时，会引发“堆栈溢出”问题。

## 块级作用域

> var缺陷以及为什么要引入let和const？

### 作用域（scope）

**作用域是指在程序中定义变量的区域，该位置决定了变量的生命周期**。通俗地理解，作用域就是变量与函数的可访问范围，即作用域控制着变量和函数的可见性和生命周期。

在 ES6 之前，ES 的作用域只有两种：**全局作用域和函数作用域**。

* **全局作用域中的对象在代码中的任何地方都能访问**，其生命周期伴随着页面的生命周期。
* **函数作用域就是在函数内部定义的变量或者函数，并且定义的变量或者函数只能在函数内部被访问**。函数执行结束之后，函数内部定义的变量会被销毁。

在 ES6 之后，JavaScript有了**块级作用域**，而其他语言都普遍支持块级作用域。**块级作用域就是使用一对大括号包裹的一段代码**，比如函数、判断语句、循环语句，甚至单独的一个{}都可以被看作是一个块级作用域。

```js
//if 块
if(1){}
//while 块
while(1){}
// 函数块
function foo(){}
//for 循环块
for(let i = 0; i<100; i++){}
// 单独一个块
{}
```

和 Java、C/C++ 不同，ES6 之前是不支持块级作用域的，没有了块级作用域，再把作用域内部的变量统一提升无疑是最快速、最简单的设计，不过这也直接导致了函数中的变量无论是在哪里声明的，在编译阶段都会被提取到执行上下文的变量环境中，所以这些变量在整个函数体内部的任何地方都是能被访问的，这也就是 JavaScript 中的变量提升。

### 变量提升所带来的问题

由于变量提升作用，使用 JavaScript 来编写和其他语言相同逻辑的代码，都有可能会导致不一样的执行结果。那为什么会出现这种情况呢？主要有以下两种原因。

#### 1. 变量容易在不被察觉的情况下被覆盖掉

比如下面的JavaScript代码：

```js
var myname = "ramona";
function showName(){  
  console.log(myname);  
  if(0){   
    var myname = "raymond"  
    }  
  console.log(myname);
}
showName(); // undefined
```

执行之后，打印出来的是 `undefined`，结合调用栈的知识，当showName 函数的执行上下文创建后，JavaScript 引擎便开始执行 showName 函数内部的代码了。

首先执行的是：`console.log(myname)`，执行这段代码需要使用变量 myname，结合调用栈状态图，我们可以知道有两个myname 变量：一个在全局执行上下文中，其值是“ramona”；另外一个在showName 函数的执行上下文中，其值是 undefined。

函数执行过程中，JavaScript 会优先从当前的执行上下文中查找变量，由于变量提升，当前的执行上下文中就包含了变量 myname，而值是 undefined，所以获取到的 myname 的值就是 undefined。

#### 2. 本应销毁的变量没有被销毁

如下代码所示：

```js
function foo(){  
  for (var i = 0; i < 7; i++) {  }  
  console.log(i); 
}
foo();
```

在使用 C 语言或者其他的大部分语言实现类似代码，在 for 循环结束之后，i 就已经被销毁了，但是在 JavaScript 代码中，i 的值并未被销毁，最后打印出来的是 7。这同样也是由变量提升而导致的，**在创建执行上下文阶段，变量 i 就已经被提升了**，所以当for 循环结束之后，变量 i 并没有被销毁。

### ES6 解决变量提升带来的缺陷

上面我们介绍了变量提升而带来的一系列问题，为了解决这些问题，**ES6 引入了 let 和const 关键字**，从而使 JavaScript 也能像其他语言一样拥有了块级作用域。

如下边的存在变量提升的代码：

```js
function varTest() {  
  var x = 1;  
  if (true) {    
    var x = 2;  // 同样的变量!    
    console.log(x);  // 2  
  }  
  console.log(x);  // 2
}
```

在这段代码中，有两个地方都定义了变量 x，第一个地方在函数块的顶部，第二个地方在 if块的内部，由于 var 的作用范围是整个函数，所以在编译阶段，会生成如下的执行上下文：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glijyio6v3j30ug0dmaf0.jpg" alt="varTest函数执行上下文" style="zoom:50%;" />

varTest 函数的执行上下文从执行上下文的变量环境中可以看出，最终只生成了一个变量 x，函数体内所有对 x 的赋值操作都会直接改变变量环境中的 x 值。所以上述代码最后通过` console.log(x) `输出的是 2，而对于相同逻辑的代码，其他语言最后一步输出的值应该是 1，因为在 if 块里面的声明不应该影响到块外面的变量。

如果改造上边的代码，让其支持块级作用域，则改造后的代码如下：

```js
function letTest() {  
  let x = 1;  
  if (true) {    
    let x = 2;  // 不同的变量    
    console.log(x);  // 2  
  }  
  console.log(x);  // 1
}
```

执行这段代码，其输出结果就和我们的预期是一致的。这是因为 let 关键字是支持块级作用域的，所以在编译阶段，JavaScript 引擎并不会把 if 块中通过 let 声明的变量存放到变量环境中，这也就意味着在 if 块通过 let 声明的关键字，并不会提升到全函数可见。所以在 if块之内打印出来的值是 2，跳出语块之后，打印出来的值就是 1 了。因此**作用块内声明的变量不影响块外面的变量**。

### JavaScript 是如何支持块级作用域的

站在执行上下文的角度来对JavaScript支持块级作用域进行分析，如下代码：

```js
function foo(){    
  var a = 1;    
  let b = 2;    
  {      
    let b = 3;      
    var c = 4;      
    let d = 5;      
    console.log(a);     
    console.log(b);    
  }    
  console.log(b);    
  console.log(c);   
  console.log(d);
}   
foo();
```

当执行上面这段代码的时候，JavaScript 引擎会先对其进行编译并创建执行上下文，然后再按照顺序执行代码，分析代码的执行过程。

第一步是编译并创建执行上下文，如下图：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gliqrqgk79j30te0leq85.jpg" style="zoom:50%;" />

*  函数内部通过 **var 声明的变量**，在编译阶段全都被**存放到变量环境**里面。
* 通过 **let 声明的变量**，在编译阶段会被**存放到词法环境**（Lexical Environment）中。
* 在**函数的作用域内部**，通过 **let 声明的变量并没有被存放到词法环境**中。

第二部继续执行代码，当执行到代码块里面时，变量环境中 a 的值已经被设置成了 1，词法环境中 b 的值已经被设置成了 2，这时候函数的执行上下文就如下图所示：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gliqtizxbjj30re0kaaf4.jpg" style="zoom:50%;" />

从图中可以看出，当进入函数的作用域块时，作用域块中通过 let 声明的变量，会被存放在词法环境的一个单独的区域中，这个区域中的变量并不影响作用域块外面的变量，比如在作用域外面声明了变量 b，在该作用域块内部也声明了变量 b，当执行到作用域内部时，它们都是独立的存在。

> 在词法环境内部，维护了一个小型栈结构，栈底是函数最外层的变量，进入一个作用域块后，就会把该作用域块内部的变量压到栈顶；当作用域执行完成之后，该作用域的信息就会从栈顶弹出，这就是词法环境的结构。(这里所讲的变量是指通过 let 或者 const 声明的变量)

接下来，当执行到作用域块中的`console.log(a)`这行代码时，就需要在词法环境和变量环境中查找变量 a 的值了，具体查找方式是：**沿着词法环境的栈顶向下查询，如果在词法环境中的某个块中查找到了，就直接返回给 JavaScript 引擎，如果没有查找到，那么继续在变量环境中查找**。这样一个变量查找过程就完成了。

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glir4rp1ygj30u00ewwji.jpg" alt="变量查找过程" style="zoom:50%;" />

当作用域块执行结束之后，其内部定义的变量就会从词法环境的栈顶弹出，最终执行上下文如下图所示：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glir61gzanj30se0hstd0.jpg" alt="作用域执行完成" style="zoom:50%;" />

小结：**块级作用域就是通过词法环境的栈结构来实现的，而变量提升是通过变量环境来实现**，通过这两者的结合，JavaScript 引擎也就同时支持了变量提升和块级作用域了。

如下面代码：

```js
let myName = 'ramona';
{
  console.log(myName);
  let myName = 'raymond';
}
```

![image-20201210145255591](https://tva1.sinaimg.cn/large/0081Kckwly1glirjs76mej31e402yq3v.jpg)

执行之后报错，在代码块内，使用let命令声明变量之前，该变量是不可用的，即暂时性死区。

## 作用域链和闭包

> 代码中出现相同的变量，JavaScript引擎是如何选择的？

### 作用域链

在每个执行上下文的变量环境中，都包含了一个外部引用，用来指向外部的执行上下文，我们把这个**外部引用称为outer**。

当一段代码使用了一个变量时，javaScript引擎首先会在”当前执行上下文“中查找该变量。如下方代码：

```js
function bar() {    
  console.log(myName);
}
function foo() {    
  var myName = " 极客邦 ";
  bar();
}
var myName = " 极客时间 ";
foo();
```

在查找 myName 变量时，如果在当前的变量环境中没有查找到，那么JavaScript 引擎会继续在 outer 所指向的执行上下文中查找，如下图：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glk3kmryuzj30wi0nejxs.jpg" style="zoom:50%;" />

从图中可以看出，bar 函数和 foo 函数的 outer 都是指向全局上下文的，这也就意味着如果在 bar 函数或者 foo 函数中使用了外部变量，那么 JavaScript 引擎会去全局执行上下文中查找。我们把这个查找的链条就称为**作用域链**。

**问题**：foo 函数调用的bar 函数，那为什么 bar 函数的外部引用是全局执行上下文，而不是 foo 函数的执行上下文？

要解决这个问题，我们需要了解词法作用域，因为在JavaScript执行过程中，作用域链是由词法作用域决定的。

### 词法作用域

**词法作用域就是指作用域是由代码中函数声明的位置来决定的**，所以**词法作用域是静态的作用域**，通过它就能够预测代码在执行过程中如何查找标识符。

在上述问题中，根据词法作用域，foo 和 bar 的上级作用域都是全局作用域，所以如果 foo 或者bar 函数使用了一个它们没有定义的变量，那么它们会到全局作用域去查找。也就是说，**词法作用域是代码阶段就决定好的，和函数是怎么调用的没有关系**。

<img src="https://i.loli.net/2020/12/15/K3Qcm2hl5qU9v1k.png" style="zoom:50%;" />

从图中可以看出，词法作用域就是根据代码的位置来决定的，其中 main 函数包含了 bar函数，bar 函数中包含了 foo 函数，因为 JavaScript 作用域链是由词法作用域决定的，所以整个词法作用域链的顺序是：foo 函数作用域—>bar 函数作用域—>main 函数作用域—> 全局作用域。

### 块级作用域中的变量查找

在编写代码的时候，如果我们使用了一个在当前作用域中不存在的变量，这时 JavaScript 引擎就需要按照作用域链在其他作用域中查找该变量，如下代码：

```js
function bar() {    
    var myName = " 浏览器 ";    
    let test1 = 100;    
    if (1) {        
        let myName = "Chrome 浏览器 ";        
        console.log(test);    
    }
}
function foo() {    
    var myName = " 极客邦 ";    
    let test = 2;
    {        
        let test = 3;      
        bar();   
    }
}
var myName = " 极客时间 ";
let myAge = 10;
let test = 1;
foo();


let count = 1;
function main() {
    let count = 2;
    function bar() {
        let count = 3;
        function foo() {
            let count = 4;
        }
    }
}
```

要想得出其执行结果，那接下来我们就得站在**作用域链和词法环境的角度**来分析下其执行过程。ES6 是支持块级作用域的，当执行到代码块时，如果代码块中有 let 或者 const 声明的变量，那么变量就会存放到该函数的词法环境中。对于上面这段代码，当执行到 bar 函数内部的 if 语句块时，其调用栈的情况如下图所示：

<img src="https://i.loli.net/2020/12/15/Rz5b3ygmYAx9qNo.png" style="zoom:50%;" />

查找过程如上去标注所示，首先是在 bar 函数的执行上下文中查找，但因为 bar 函数的执行上下文中没有定义 test 变量，所以根据词法作用域的规则，下一步就在 bar 函数的外部作用域中查找，也就是全局作用域。

### 闭包

看下面这段代码。

```js
function foo() {    
    var myName = " 极客时间 "    
    let test1 = 1    
    const test2 = 2    
    var innerBar = {        
        getName:function(){            
            console.log(test1)            
            return myName        
        },        
        setName:function(newName){            
            myName = newName        
        }    
    }    
    return innerBar
}
var bar = foo()
bar.setName(" 极客邦 ")
bar.getName()
console.log(bar.getName())
```

首先看看当执行到 foo 函数内部的return innerBar这行代码时调用栈的情况，如下图：

<img src="https://i.loli.net/2020/12/15/ra8q6m3GPZSV7L5.png" style="zoom:50%;" />

从上面的代码可以看出，innerBar 是一个对象，包含了 getName 和 setName 的两个方法（通常我们把对象内部的函数称为方法）。可以看到，这两个方法都是在 foo 函数内部定义的，并且这两个方法内部都使用了 myName 和 test1 两个变量。

**根据词法作用域的规则，内部函数 getName 和 setName 总是可以访问它们的外部函数foo 中的变量，**所以当 innerBar 对象返回给全局变量 bar 时，虽然 foo 函数已经执行结束，但是 getName 和 setName 函数依然可以使用 foo 函数中的变量 myName 和test1。所以当 foo 函数执行完成之后，其整个调用栈的状态如下图所示：

<img src="https://i.loli.net/2020/12/15/W3uGbsKghp5L2NQ.png" style="zoom:50%;" />

从上图可以看出，foo 函数执行完成之后，其执行上下文从栈顶弹出了，但是由于返回的setName 和 getName 方法中使用了 foo 函数内部的变量 myName 和 test1，所以这两个变量依然保存在内存中。



在 JavaScript 中，根据词法作用域的规则，**内部函数总是可以访问其外部函数中声明的变量**，当通过调用一个外部函数返回一个内部函数后，即使该外部函数已经执行结束了，但是内部函数引用外部函数的变量依然保存在内存中，我们就把这些变量的集合称为**闭包**。比如外部函数是 foo，那么这些变量的集合就称为 foo 函数的闭包。

当执行到 bar.setName 方法中的myName = "极客邦"这句代码时，JavaScript 引擎会沿着“当前执行上下文–>foo 函数闭包–> 全局执行上下文”的顺序来查找 myName 变量，如下面的调用栈状态图：

<img src="https://i.loli.net/2020/12/15/OlozuTtNFevbWaw.png" style="zoom:50%;" />

从图中可以看出，setName 的执行上下文中没有 myName 变量，foo 函数的闭包中包含了变量 myName，所以调用 setName 时，会修改 foo 闭包中的 myName 变量的值。同样的流程，当调用 bar.getName 的时候，所访问的变量 myName 也是位于 foo 函数闭包中的。

也可以通过“开发者工具”来看看闭包的情况，打开 Chrome 的“开发者工具”，在bar 函数任意地方打上断点，然后刷新页面，可以看到如下内容：

<img src="https://i.loli.net/2020/12/15/GxFseo34KwBAYTd.png" style="zoom:50%;" />

从图中可以看出来，当调用 bar.getName 的时候，右边 `Scope `项就体现出了作用域链的情况：Local 就是当前的 getName 函数的作用域，`Closure(foo) `是指 foo 函数的闭包，最下面的 Global 就是指全局作用域，从“`Local–>Closure(foo)–>Global`”就是一个完整的作用域链。

#### 闭包回收

通常，如果**引用闭包的函数是一个全局变量**，那么闭包会一直存在直到页面关闭；但如果这个闭包以后不再使用的话，就会造成**内存泄漏**。

如果**引用闭包的函数是个局部变量**，等函数销毁后，在下次 JavaScript 引擎执行垃圾回收时，判断闭包这块内容如果已经**不再被使用**，那么 **JavaScript 引擎的垃圾回收器就会回收这块内存**。

所以在使用闭包的时候，要尽量注意一个原则：**如果该闭包会一直使用，那么它可以作为全局变量而存在；但如果使用频率不高，而且占用内存又比较大的话，那就尽量让它成为一个局部变量。**

## this

> 从JavaScript执行上下文的视角看待this

在执行上下文中包含了四种环境，分别是：**变量环境、词法环境、外部环境和this**，this 也是和执行上下文绑定的，也就是说每个执行上下文中都有一个this。

执行上下文主要分为三种，分别是：**全局执行上下文、函数执行上下文和 eval 执行上下文**，所以对应的 this 也只有这三种：全局执行上下文中的 this、函数中的 this 和 eval 中的 this。

> 1. 当函数作为对象的方法调用时，函数中的 this 就是该对象；
> 2. 当函数被正常调用时，在严格模式下，this 值是 undefined，非严格模式下 this 指向的是全局对象 window；
> 3. 嵌套函数中的 this 不会继承外层函数的 this 值。
> 4. 箭头函数没有自己的执行上下文，所以箭头函数的this 就是它外层函数的 this。

### 全局执行上下文中的 this

我们可以在浏览器控制台输入`console.log(this)`来打印全局上下文中的this，输出的是window对象，因此，**全局只执行上下文中的this是指向window对象的**。这也是 this 和作用域链的唯一交点，作用域链的最底端包含了 window对象，全局执行上下文中的 this 也是指向 window 对象。

### 函数执行上下文中的 this

执行下面代码：

```js
function foo() {
    console.log(this);
}
foo();
```

输出的值仍然是window对象，这说明在默认情况下调用一个函数，其执行上下文中的 this 也是指向 window 对象的。那如何设置执行上下文中的this来指向其他的对象呢？通常情况下，有下面三种方式来设置函数执行上下文中的 this 值。

####  通过函数的 call 方法设置

可以通过函数的call方法来设置函数执行上下文的 this 指向，比如下面这段代码，我们就并没有直接调用 foo 函数，而是调用了 foo 的 call 方法，并将 bar 对象作为 call 方法的参数。

```js
let bar = {  
    myName : " 极客邦 ",  
    test1 : 1
}
function foo(){  
    this.myName = " 极客时间 "
}
foo.call(bar)
console.log(bar)
console.log(myName)
```

![](https://i.loli.net/2020/12/15/fowqmDxaNTyRKsc.png)

执行代码后发现，foo 函数内部的 this 已经指向了 bar 对象，因为通过打印 bar 对象，可以看出 bar 的 myName 属性已经由“极客邦”变为“极客时间”了，同时在全局执行上下文中打印 myName，JavaScript 引擎提示该变量未定义。

当然，除了 call 方法，你还可以使用bind和apply方法来设置函数执行上下文中的 this。

#### 2. 通过对象调用方法设置

要改变函数执行上下文中的 this 指向，除了通过函数的 call 方法来实现外，还可以通过对象调用的方式，比如下面这段代码：

```js
var myObj = {  
    name : " 极客时间 " ,  
    showThis: function(){    
        console.log(this)  
    }
}
myObj.showThis()
```

执行代码，输出的this是指向myObj的。![](https://i.loli.net/2020/12/15/xamMv6AZyQdNqRw.png)

**结论**：**使用对象来调用其内部的一个方法，该方法的 this 是指向对象本身的**。

如果我们改变一下调用方式，把 showThis 赋给一个全局对象，然后再调用该对象，代码如下所示：

```js
var myObj = {  
    name : " 极客时间 " ,  
    showThis: function(){
        this.name = "极客邦"
        console.log(this)  
    }
}
var foo = myObj.showThis
foo()
```

执行代码会发现，this又指向了全局window对象。

##### 总结

* **在全局环境中调用一个函数，函数内部的 this 指向的是全局变量 window。**
* **通过一个对象来调用其内部的一个方法，该方法的执行上下文中的 this 指向对象本身。**

#### 3. 通过构造函数中设置

也可以像下面的示例代码这样设置构造函数中的 this：

```js
function CreateObj(){  
    this.name = " 极客时间 "
}
var myObj = new CreateObj()
```

当执行 new CreateObj() 的时候，JavaScript 引擎做了如下四件事：

* 首先**创建了一个空对象** tempObj；
* 接着**调用 CreateObj.call 方法**，并将 tempObj 作为 call 方法的参数，这样当CreateObj 的执行上下文创建时，它的 this 就指向了 tempObj 对象；
* 然后**执行 CreateObj 函数**，此时的 CreateObj 函数执行上下文中的 this 指向了tempObj 对象；
* 最后返回 tempObj 对象。

这样，我们就通过 new 关键字构建好了一个新对象，并且构造函数中的 this 其实就是新对象本身。

### this 的设计缺陷以及应对方案

#### 1. 嵌套函数中的 this 不会从外层函数中继承

如下面代码：

```js
var myObj = {  
    name : " 极客时间 " ,  
    showThis: function(){    
        console.log(this)  
        function bar () {
            console.log(this)
        }
        bar()
    }
}
myObj.showThis()
```

执行代码后发现，函数 bar 中的 this 指向的是全局 window对象，而函数 showThis 中的 this 指向的是 myObj 对象。<img src="https://i.loli.net/2020/12/15/tCOzWgB6QJ4LKdS.png" />

（1）这个问题可以通过如下方式来解决，比如在 showThis 函数中声明一个变量 self 用来保存 this，然后在 bar 函数中使用 self，代码如下所示：

```js
var myObj = {  
    name : " 极客时间 " ,  
    showThis: function(){    
        var self = this
        console.log(self)  
        function bar () {
            console.log(self)
        }
        bar()
    }
}
myObj.showThis()
```

执行代码后发现，最终 myObj 中的 name 属性值变成了“极客邦”。其实，这个方法的的本质是**把 this 体系转换为了作用域的体系。**<img src="https://i.loli.net/2020/12/15/tCOzWgB6QJ4LKdS.png" style="zoom:50%;" />

（2）也可以使用 ES6 中的箭头函数来解决这个问题，结合下面代码：

```js
var myObj = {  
    name : " 极客时间 " ,  
    showThis: function(){    
        console.log(this)  
        var bar = () =>{
            console.log(this)
        }
        bar()
    }
}
myObj.showThis()
```

执行代码输出结果同上。这是因为 ES6 中的箭头函数并不会创建其自身的执行上下文，所以箭头函数中的 this 取决于它的外部函数。

#### 2. 普通函数中的 this 默认指向全局对象 window

在默认情况下调用一个函数，其执行上下文中的 this 是默认指向全局对象 window 的。不过这个设计也是一种缺陷，因为在实际工作中，我们并不希望函数执行上下文中的 this默认指向全局对象，因为这样会打破数据的边界，造成一些误操作。如果要让函数执行上下文中的 this 指向某个对象，最好的方式是**通过 call 方法来显示调用**。

## 编译器和解释器

> V8是如何执行一段JavaScript代码的？

要深入理解 V8 的工作原理，就需要搞清楚**编译器（Compiler）、解释器（Interpreter）、抽象语法树（AST）、字节码（Bytecode）、即时编译器（JIT）**等概念。

### 编译器和解释器

之所以存在编译器和解释器，是因为机器不能直接理解我们所写的代码，所以在执行程序之前，需要将我们所写的代码“翻译”成机器能读懂的机器语言。**按语言的执行流程，可以把语言划分为编译型语言和解释型语言。**

* **编译型语言**：**在程序执行之前，需要经过编译器的编译过程，并且编译之后会直接保留机器能读懂的二进制文件**，这样每次运行程序时，都可以直接运行该二进制文件，而不需要再次重新编译了。比如 C/C++、GO 等都是编译型语言。
* **解释型语言**：用解释性语言编写的程序，**在每次运行时都需要通过解释器对程序进行动态解释和执行**。比如 Python、JavaScript 等都属于解释型语言。

下图为编译器和解释器“翻译”代码的执行流程图：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glwu8b4cfdj30vk0ds77a.jpg" style="zoom:50%;" />

从图中你可以看出这二者的执行流程，大致可阐述为如下：

1、在编译型语言的编译过程中，编译器首先会依次对源代码进行词法分析、语法分析，生成抽象语法树（AST），然后是优化代码，最后再生成处理器能够理解的机器码。如果编译成功，将会生成一个可执行的文件。但如果编译过程发生了语法或者其他的错误，那么编译器就会抛出异常，最后的二进制文件也不会生成成功。

2、在解释型语言的解释过程中，同样解释器也会对源代码进行词法分析、语法分析，并生成抽象语法树（AST），不过它会再基于抽象语法树生成字节码，最后再根据字节码来执行程序、输出结果。

### V8 是如何执行一段 JavaScript 代码的

如下图，V8 执行一段代码流程图从图中可以清楚地看到，V8 在执行过程中**既有解释器 Ignition，又有编译器 TurboFan。**

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glwvg6hkiyj30tk0e2af5.jpg" style="zoom:50%;" />

那么它们是如何配合去执行一段 JavaScript 代码的呢? 下面我们就按照上图来一一分解其执行流程。

#### 1. 生成抽象语法树（AST）和执行上下文

将源代码转换为抽象语法树，并生成执行上下文，而执行上下文主要是代码在执行过程中的环境信息。

高级语言是开发者可以理解的语言，但是让编译器或者解释器来理解就非常困难了。对于编译器或者解释器来说，它们可以理解的就是 AST 了。所以无论你使用的是解释型语言还是编译型语言，在编译过程中，它们都会生成一个 AST。这和渲染引擎将 HTML 格式文件转换为计算机可以理解的 DOM 树的情况类似。

比如下面这段代码：

```js
var myName = " 极客时间 "
function foo(){  
  return 23;
}
myName = "geektime"
foo()
```

这段代码经过javascript-ast站点处理后，生成的 AST 结构如下：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glwwbxhq9zj30oe14ajvi.jpg" style="zoom:50%;" />

从图中可以看出，AST 的结构和代码的结构非常相似，也可以把 AST 看成代码的结构化的表示，编译器或者解释器后续的工作都需要依赖于 AST，而不是源代码。

AST 是非常重要的一种数据结构，在很多项目中有着广泛的应用。其中最著名的一个项目是 Babel。**Babel 是一个被广泛使用的代码转码器，可以将 ES6 代码转为 ES5 代码**，这意味着我们可以现在就用 ES6 编写程序，而不用担心现有环境是否支持 ES6。**Babel 的工作原理就是先将 ES6 源码转换为 AST，然后再将 ES6 语法的 AST 转换为 ES5 语法的 AST，最后利用 ES5 的 AST 生成 JavaScript 源代码**。

除了 Babel 外，还有 ESLint 也使用 AST。ESLint 是一个用来检查 JavaScript 编写规范的插件，其检测流程也是需要将源码转换为 AST，然后再利用 AST 来检查代码规范化的问题。

生成AST需要两个阶段。第一阶段是分词（tokenize），第二阶段是解析（parse）。

**分词**，又称为词法分析，**其作用是将一行行的源码拆解成一个个token**。所谓token，指的是语法上不可能再分的、最小的单个字符或字符串。

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glwwsnjq62j30nq0bswh6.jpg" style="zoom:50%;" />



如上图所示，通过`var myName = “极客时间”`简单地定义了一个变量，其中关键字“var”、标识符“myName” 、赋值运算符“=”、字符串“极客时间”四个都是token，而且它们代表的属性还不一样。

**解析**，又称为语法分析，**其作用是将上一步生成的 token 数据，根据语法规则转为 AST**。如果源码符合语法规则，这一步就会顺利完成。但如果源码存在语法错误，这一步就会终止，并抛出一个“语法错误”。

这就是 AST 的生成过程，先分词，再解析。有了 AST 后，那接下来 V8 就会生成该段代码的执行上下文。

#### 2. 生成字节码

有了 AST 和执行上下文后，那接下来的第二步，解释器 Ignition 就会根据 AST生成字节码，并解释执行字节码。

其实一开始 V8 并没有字节码，而是直接将 AST 转换为机器码，由于执行机器码的效率是非常高效的，所以这种方式在发布后的一段时间内运行效果是非常好的。但是随着Chrome 在手机上的广泛普及，特别是运行在 512M 内存的手机上，内存占用问题也暴露出来了，因为 V8 需要消耗大量的内存来存放转换后的机器码。为了解决内存占用问题，V8 团队大幅重构了引擎架构，引入字节码，并且抛弃了之前的编译器，最终花了将进四年的时间，实现了现在的这套架构。

**字节码就是介于 AST 和机器码之间的一种代码。但是与特定类型的机器码无关，字节码需要通过解释器将其转换为机器码后才能执行。**

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glwy2eci8hj30rc072gnx.jpg" alt="字节码和机器码占用空间对比" style="zoom:50%;" />

从图中可以看出，机器码所占用的空间远远超过了字节码，所以使用字节码可以减少系统的内存使用。

#### 3. 执行代码

生成字节码之后，接下来就要进入执行阶段了。

通常，如果有一段第一次执行的字节码，解释器 Ignition 会逐条解释执行。在执行字节码的过程中，如果发现有热点代码（HotSpot），比如一段代码被重复执行多次，这种就称为热点代码，那么后台的编译器 TurboFan 就会把该段热点的字节码编译为高效的机器码，然后当再次执行这段被优化的代码时，只需要执行编译后的机器码就可以了，这样就大大提升了代码的执行效率。

> 解释器 Ignition 是点火器的意思，编译器TurboFan 是涡轮增压的意思，寓意着代码启动时通过点火器慢慢发动，一旦启动，涡轮增压介入，其执行效率随着执行时间越来越高效率，因为热点代码都被编译器 TurboFan 转换了机器码，直接执行机器码就省去了字节码“翻译”为机器码的过程。

其实在 Java 和 Python 的虚拟机也都是基于字节码配合解释器和编译器实现的，我们把这种技术称为**即时编译（JIT）**。具体到 V8，就是指解释器 Ignition 在解释执行字节码的同时，收集代码信息，当它发现某一部分代码变热了之后，TurboFan 编译器便闪亮登场，把热点的字节码转换为机器码，并把转换后的机器码保存起来，以备下次使用。对于 JavaScript 工作引擎，除了 V8 使用了“字节码 +JIT”技术之外，苹果的SquirrelFish Extreme 和 Mozilla 的 SpiderMonkey 也都使用了该技术。

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1glwygq1n7bj30j40u6adn.jpg" style="zoom:50%;" />

### JavaScript 的性能优化

对于优化 JavaScript 执行效率，**应该将优化的中心聚焦在单次脚本的执行时间和脚本的网络下载上**，主要关注以下三点内容：

1、提升单次脚本的执行速度，避免 JavaScript 的长任务霸占主线程，这样可以使得页面快速响应交互；

2、避免大的内联脚本，因为在解析 HTML 的过程中，解析和编译也会占用主线程；

3、减少 JavaScript 文件的容量，因为更小的文件会提升下载速度，并且占用更低的内存。

