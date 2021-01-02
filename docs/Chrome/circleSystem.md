## 消息队列和事件循环

> 页面是怎么“活起来”的？

每个渲染进程都有一个主线程，并且主线程非常繁忙，既要处理 DOM，又要计算样式，还要处理布局，同时还需要处理 JavaScript 任务以及各种输入事件。要让这么多不同类型的任务在主线程中有条不紊地执行，这就需要一个系统来统筹调度这些任务，这个统筹调度系统就是消息队列和事件循环系统。

这篇文章从简单的场景分析，然后一步步了解浏览器页面主线程是如何运作的。

#### 使用单线程处理安排好的任务

比如要在一个线程中执行多个任务，我们需要将所有这些任务按顺序放进主线程中，等线程执行时，这些任务会按照顺序在线程中依次被执行，等所有任务执行完成之后，线程会自动退出。

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gm0czpkocwj30tq0bs0y8.jpg" style="zoom:50%;" />

#### 在线程运行过程中处理新任务

但并不是所有的任务都是在执行之前统一安排好的，大部分情况下，新的任务是在线程运行过程中产生的。比如在线程执行过程中，又接收到了一个新的任务要求，那上面那种方式就无法处理这种情况了。

要想在线程运行过程中，能接收并执行新的任务，就需要采用**事件循环机制**。我们可以通过一个 for 循环语句来监听是否有新任务。

相较于第一版的线程，这一版的线程做了两点改进。

* 引入了**循环机制**，具体实现方式是在线程语句最后添加了一个**for 循环语句**，线程会一直循环执行。
* 引入了**事件**，可以在线程运行过程中，等待用户输入信息，等待过程中线程处于暂停状态，一旦接收到用户输入的信息，那么线程会被激活，然后执行相应运算，最后输出结果。

通过引入事件循环机制，就可以让该线程“活”起来了，我们可以结合下图来参考下这个改进版的线程。

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gm0d15o3caj30su0hk425.jpg" style="zoom:50%;" />

#### 处理其他线程发送过来的任务

在上面的线程模型中，所有的任务都是来自于线程内部，如果另外一个线程想让主线程执行一个任务，利用第二版的线程模型是无法做到的。那下面就来看看其他线程是如何发送消息给渲染主线程的，具体形式参考下图：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gm0d79m5blj30s60iigq8.jpg" style="zoom:50%;" />

从上图可以看出，渲染主线程会频繁接收到来自于 IO 线程的一些任务，接收到这些任务之后，渲染进程就需要着手处理，比如接收到资源加载完成的消息后，渲染进程就要着手进行DOM 解析了；接收到鼠标点击的消息后，渲染主线程就要开始执行相应的 JavaScript 脚本来处理该点击事件。

如何设计好一个线程模型，能让其能够接收其他线程发送的消息呢？一个通用模式是使用**消息队列**。消息队列是一种数据结构，可以存放要执行的任务。它符合队列“**先进先出**”的特点，也就是说要添加任务的话，添加到队列的尾部；要取出任务的话，从队列头部去取。

有了队列之后，我们就可以继续改造线程模型了，改造方案如下图所示：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gm0dqe021oj30wo0jegsl.jpg" style="zoom:50%;" />

改造步骤：

1. 添加一个消息队列；
2. IO 线程中产生的新任务添加进消息队列尾部；
3. 渲染主线程会循环地从消息队列头部中读取任务，执行任务。

#### 处理其他进程发送过来的任务

通过使用消息队列，我们实现了线程之间的消息通信。在 Chrome 中，跨进程之间的任务也是频繁发生的，那么如何处理其他进程发送过来的任务？可以参考下图：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gm0e27satbj30wg0n6qau.jpg" style="zoom:50%;" />

从图中可以看出，**渲染进程专门有一个 IO 线程用来接收其他进程传进来的消息**，接收到消息之后，会将这些消息组装成任务发送给渲染主线程。

#### 消息队列中的任务类型

消息队列中有很多内部消息类型，如输入事件（鼠标滚动、点击、移动）、微任务、文件读写、WebSocket、JavaScript 定时器等等。除此之外，消息队列中还包含了很多与页面相关的事件，如 JavaScript 执行、解析DOM、样式计算、布局计算、CSS 动画等。

以上这些事件都是在主线程中执行的，所以在编写 Web 应用时，还需要衡量这些事件所占用的时长，并想办法解决单个任务占用主线程过久的问题。

#### 如何安全退出

当页面主线程执行完成之后，Chrome为保证页面主线程能够安全退出，会在页面主线程设置一个退出标志的变量，在每次执行完一个任务时，判断是否有设置退出标志。如果设置了，那么就直接中断当前的所有任务，退出线程。

#### 页面使用单线程的缺点

页面线程所有执行的任务都来自于消息队列。消息队列是“先进先出”的属性，也就是说放入队列中的任务，需要等待前面的任务被执行完，才会被执行。鉴于这个属性，就有如下两个问题需要解决。

##### 问题一是如何处理高优先级的任务。

比如一个典型的场景是监控 DOM 节点的变化情况（节点的插入、修改、删除等动态变化），然后根据这些变化来处理相应的业务逻辑。一个通用的设计的是，利用 JavaScript设计一套监听接口，当变化发生时，渲染引擎同步调用这些接口，这是一个典型的观察者模式。

不过这个模式有个问题，因为 DOM 变化非常频繁，如果每次发生变化的时候，都直接调用相应的 JavaScript 接口，那么这个当前的任务执行时间会被拉长，从而导致执行效率的下降。

如果将这些 DOM 变化做成异步的消息事件，添加到消息队列的尾部，那么又会影响到监控的实时性，因为在添加到消息队列的过程中，可能前面就有很多任务在排队了。

这也就是说，如果 DOM 发生变化，采用同步通知的方式，会影响当前任务的执行效率；如果采用异步方式，又会影响到监控的实时性。那该如何权衡效率和实时性呢？

针对这种情况，微任务就应用而生了，那微任务是如何权衡效率和实时性的？通常我们把**消息队列中的任务称为宏任务，每个宏任务中都包含了一个微任务队列**，在执行宏任务的过程中，如果 DOM 有变化，那么就会将该变化添加到微任务列表中，这样就不会影响到宏任务的继续执行，因此也就解决了执行效率的问题。

等宏任务中的主要功能都直接完成之后，这时候，渲染引擎并不着急去执行下一个宏任务，而是执行当前宏任务中的微任务，因为 DOM 变化的事件都保存在这些微任务队列中，这样也就解决了实时性问题。

##### 问题二是如何解决单个任务执行时长过久的问题。

因为所有的任务都是在单线程中执行的，所以每次只能执行一个任务，而其他任务就都处于等待状态。如果其中一个任务执行时间过久，那么下一个任务就要等待很长时间。可以参考下图：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gm0esmbr9xj30ta0b8q5z.jpg" style="zoom:50%;" />

从图中你可以看到，如果在执行动画过程中，其中有个 JavaScript 任务因执行时间过久，占用了动画单帧的时间，这样会给用户制造了卡顿的感觉，这当然是极不好的用户体验。针对这种情况，JavaScript 可以通过**回调功能**来规避这种问题，也就是让要执行的JavaScript 任务滞后执行。

#### 浏览器页面是如何运行的

可以打开开发者工具，点击“Performance”标签，选择左上角的“start porfiling andload page”来记录整个页面加载过程中的事件执行情况，如下图所示：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gm0exa99vdj315i0pownq.jpg" style="zoom:50%;" />

从图中可以看出，我们点击展开了 Main 这个项目，其记录了主线程执行过程中的所有任务。图中灰色的就是一个个任务，每个任务下面还有子任务，其中的 Parse HTML 任务，是把 HTML 解析为 DOM 的任务。值得注意的是，在执行 Parse HTML 的时候，如果遇到JavaScript 脚本，那么会暂停当前的 HTML 解析而去执行 JavaScript 脚本。


## WebAPI：setTimeout

> setTimeout是如何实现的？

浏览器页面是由消息队列和事件循环系统来驱动的。

 `setTimeout` 是一个**定时器，用来指定某个函数在多少毫秒之后执行**。它会返回一个整数，表示定时器的编号，同时你还可以通过该编号来取消这个定时器。下面的示例代码就演示了定时器最基础的使用方式：

```js
function showName() {
  console.log("ramona");
}
var timerID = setTimeout(showName, 200);
```

执行上述代码，通过 setTimeout 指定在 200 毫秒之后调用showName 函数，并输出ramona。

### 浏览器怎么实现 setTimeout

我们知道渲染进程中所有运行在主线程上的任务都需要先添加到消息队列，然后事件循环系统再按照顺序执行消息队列中的任务。

> 典型的事件：
>
> * 当接收到 HTML 文档数据，渲染引擎就会将“解析 DOM”事件添加到消息队列中；
> * 当用户改变了 Web 页面的窗口大小，渲染引擎就会将“重新布局”的事件添加到消息队列中；
> * 当触发了 JavaScript 引擎垃圾回收机制，渲染引擎会将“垃圾回收”任务添加到消息队列中；
> * 如果要执行一段异步 JavaScript 代码，也是需要将执行任务添加到消息队列中。

所以说要执行一段异步任务，需要先将任务添加到消息队列中。不过通过定时器设置回调函数有点特别，它们需要在指定的时间间隔内被调用，但消息队列中的任务是按照顺序执行的，所以为了保证回调函数能在指定时间内执行，你不能将定时器的回调函数直接添加到消息队列中。

那么在消息循环系统的基础之上加上定时器的功能，会如何设计？在 Chrome 中除了正常使用的消息队列之外，还有另外一个消息队列，这个队列中维护了需要延迟执行的任务列表，包括了定时器和 Chromium 内部一些需要延迟执行的任务。所以当通过 JavaScript 创建一个定时器时，渲染进程会将该定时器的回调任务添加到延迟队列中。

源码中延迟执行队列的定义如下所示：

```js
DelayedIncomingQueue delayed_incoming_queue;
```

当通过 JavaScript 调用 setTimeout 设置回调函数的时候，渲染进程将会创建一个回调任务，包含了回调函数 showName、当前发起时间、延迟执行时间，其模拟代码如下所示：

```js
struct DelayTask{  
  int64 id；  
  CallBackFunction cbf;  
  int start_time;  
  int delay_time;
};
DelayTask timerTask;
timerTask.cbf = showName;
timerTask.start_time = getCurrentTime(); // 获取当前时间
timerTask.delay_time = 200;// 设置延迟执行时间
```

创建好回调任务之后，再将该任务添加到延迟执行队列中，代码如下所示：

```js
delayed_incoming_queue.push(timerTask)；
```

现在通过定时器发起的任务就被保存到延迟队列中了，那接下来我们再来看看消息循环系统是怎么触发延迟队列的。

在消息循环的代码中加入执行延迟队列的代码，如下所示：

```js
void ProcessTimerTask(){  
  // 从 delayed_incoming_queue 中取出已经到期的定时器任务  
  // 依次执行这些任务
}
TaskQueue task_queue；
void ProcessTask();
bool keep_running = true;
void MainTherad(){  
  for(;;){    
    // 执行消息队列中的任务    
    Task task = task_queue.takeTask();
    ProcessTask(task);    // 执行延迟队列中的任务 
    ProcessDelayTask()    
    if(!keep_running) // 如果设置了退出标志，那么直接退出线程循环        
      break;   
  }
}
```

从上面代码可以看出来，我们添加了一个`ProcessDelayTask` 函数，该函数是专门用来处理延迟执行任务的。这里我们要重点关注它的执行时机，在上段代码中，处理完消息队列中的一个任务之后，就开始执行 ProcessDelayTask 函数。ProcessDelayTask 函数会根据发起时间和延迟时间计算出到期的任务，然后依次执行这些到期的任务。等到期的任务执行完成之后，再继续下一个循环过程。通过这样的方式，一个完整的定时器就实现了。

设置一个定时器，JavaScript 引擎会返回一个定时器的 ID。那通常情况下，当一个定时器的任务还没有被执行的时候，也是可以取消的，具体方法是调用clearTimeout 函数，并传入需要取消的定时器的 ID。如下面代码所示：

```js
clearTimeout(timer_id)
```

其实浏览器内部实现取消定时器的操作也是非常简单的，就是直接从delayed_incoming_queue 延迟队列中，通过 ID 查找到对应的任务，然后再将其从队列中删除掉就可以了。

### 使用 setTimeout 的一些注意事项

##### 1、如果当前任务执行时间过久，会影延迟到期定时器任务的执行

在使用 setTimeout 的时候，有很多因素会导致回调函数执行比设定的预期值要久，其中一个就是当前任务执行时间过久从而导致定时器设置的任务被延后执行。我们先看下面这段代码：

```js
function bar() {    
  console.log('bar')
}
function foo() {    
  setTimeout(bar, 0);    
  for (let i = 0; i < 5000; i++) {        
    let i = 5+8+8+8        
    console.log(i)    
  }
}
```

这段代码中，在执行 foo 函数的时候使用 setTimeout 设置了一个 0 延时的回调任务，设置好回调任务后，foo 函数会继续执行 5000 次 for 循环。通过 setTimeout 设置的回调任务被放入了消息队列中并且等待下一次执行，这里并不是立即执行的；要执行消息队列中的下个任务，需要等待当前的任务执行完成，由于当前这段代码要执行 5000 次的 for 循环，所以当前这个任务的执行时间会比较久一点。这势必会影响到下个任务的执行时间。

也可以打开 Performance 来看看其执行过程，如下图所示：

![截屏2021-01-01 22.04.00](https://tva1.sinaimg.cn/large/0081Kckwly1gm8jne11w9j30xe0f845k.jpg)

从图中可以看到，执行 foo 函数所消耗的时长是 500 毫秒，这也就意味着通过setTimeout 设置的任务会被推迟到 500 毫秒以后再去执行，而设置 setTimeout 的回调延迟时间是 0。

##### 2. 如果 setTimeout 存在嵌套调用，那么系统会设置最短时间间隔为 4 毫秒

也就是说在定时器函数里面嵌套调用定时器，也会延长定时器的执行时间，可以先看下面的这段代码：

```js
function cb() { 
  setTimeout(cb, 0); 
}
setTimeout(cb, 0);
```

还是可以通过 Performance 来记录下这段代码的执行过程，如下图所示:

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gm8jqx0dwyj30yu0d6ta3.jpg" style="zoom:50%;" />

上图中的竖线就是定时器的函数回调过程，从图中可以看出，前面五次调用的时间间隔比较小，嵌套调用超过五次以上，后面每次的调用最小时间间隔是 4 毫秒。之所以出现这样的情况，是因为在 Chrome 中，定时器被嵌套调用 5 次以上，系统会判断该函数方法被阻塞了，如果定时器的调用时间间隔小于 4 毫秒，那么浏览器会将每次调用的时间间隔设置为4 毫秒。

所以，一些实时性较高的需求就不太适合使用 setTimeout 了，比如你用 setTimeout 来实现 JavaScript 动画就不是一个很好的主意。

##### 3. 未激活的页面，setTimeout 执行最小间隔是 1000 毫秒

除了前面的 4 毫秒延迟，还有一个很容易被忽略的地方，那就是未被激活的页面中定时器最小值大于 1000 毫秒，也就是说，如果标签不是当前的激活标签，那么定时器最小的时间间隔是 1000 毫秒，目的是为了优化后台页面的加载损耗以及降低耗电量。这一点在使用定时器的时候要注意。

##### 4. 延时执行时间有最大值

除了要了解定时器的回调函数时间比实际设定值要延后之外，还有一点需要注意下，那就是Chrome、Safari、Firefox 都是以 32 个 bit 来存储延时值的，32bit 最大只能存放的数字是 2147483647 毫秒，这就意味着，如果 setTimeout 设置的延迟值大于 2147483647 毫秒（大约 24.8 天）时就会溢出，这导致定时器会被立即执行。你可以运行下面这段代码：

```js
function showName(){  
  console.log(" 极客时间 ")
}
var timerID = setTimeout(showName,2147483648);// 会被理解调用执行
```

运行后可以看到，这段代码是立即被执行的。但如果将延时值修改为小于 2147483647 毫秒的某个值，那么执行时就没有问题了。

##### 5. 使用 setTimeout 设置的回调函数中的 this 不符合直觉

如果被 setTimeout 推迟执行的回调函数是某个对象的方法，那么该方法中的 this 关键字将指向全局环境，而不是定义时所在的那个对象。这点在前面介绍 this 的时候也提过，你可以看下面这段代码的执行结果：

```js
var name= 1;
var MyObj = {  
  name: 2,  
  showName: function(){    
    console.log(this.name);  
  }
}
setTimeout(MyObj.showName,1000)
```

这里输出的是 1，因为这段代码在编译的时候，执行上下文中的 this 会被设置为全局window，如果是严格模式，会被设置为 undefined。

那解决这个问题通常可以使用下面这两种方法。

第一种是将MyObj.showName放在匿名函数中执行，如下所示：

```js
// 箭头函数
setTimeout(() => {    
  MyObj.showName()
}, 1000);
// 或者 function 函数
setTimeout(function() {  
  MyObj.showName();
}, 1000)
```

第二种是使用 bind 方法，将 showName 绑定在 MyObj 上面，代码如下所示:

```js
setTimeout(MyObj.showName.bind(MyObj), 1000)
```

## WebAPI：XMLHttpRequest

> XMLHttpRequest是怎么实现的?

在 XMLHttpRequest 出现之前，如果服务器数据有更新，依然需要重新刷新整个页面。而 **XMLHttpRequest 提供了从 Web 服务器获取数据的能力**，如果你想要更新某条数据，只需要通过 XMLHttpRequest 请求服务器提供的接口，就可以获取到服务器的数据，然后再操作 DOM 来更新页面内容，整个过程只需要更新网页的一部分就可以了，而不用像之前那样还得刷新整个页面，这样既有效率又不会打扰到用户。

### 回调函数（Callback Function）VS 系统调用栈

将一个函数作为参数传递给另外一个函数，那作为参数的这个函数就是回调函数。简化的代码如下所示：

```js
let callback = function() {
  console.log('I am do homework');
}
function dowork(cb) {
  console.log('start do work');
  cb();
  console.log('end do work');
}
dowork(callback);
// start do work
// I am do homework
// end do work
```

在上面示例代码中，我们将一个匿名函数赋值给变量 callback，同时将 callback 作为参数传递给了 doWork() 函数，这时在函数 doWork() 中 callback 就是回调函数。

上面的回调方法有个特点，就是**回调函数 callback 是在主函数 doWork 返回之前执行的，我们把这个回调过程称为同步回调**。

下面看异步回调的例子：

```js
let callback = function() {
  console.log('I am do homework');
}
function dowork(cb) {
  console.log('start do work');
  setTimeout(cb, 1000);
  console.log('end do work');
}
dowork(callback);
// start do work
// end do work
// I am do homework
```

在这个例子中，我们使用了 setTimeout 函数让 callback 在 doWork 函数执行结束后，又延时了 1 秒再执行，这次 callback 并没有在主函数 doWork 内部被调用，我们把这种回调函数在主函数外部执行的过程称为异步回调。

**当循环系统在执行一个任务的时候，都要为这个任务维护一个系统调用栈**。这个系统调用栈类似于 JavaScript 的调用栈，只不过系统调用栈是Chromium 的开发语言 C++ 来维护的（其完整的调用栈信息你可以通过chrome://tracing/ 来抓取）。当然，也可以通过 Performance 来抓取它核心的调用信息，如下图所示：

<img src="https://tva1.sinaimg.cn/large/0081Kckwly1gm9eq4bdimj313207u77t.jpg" style="zoom:50%;" />

这幅图记录了一个 Parse HTML 的任务执行过程，其中黄色的条目表示执行 JavaScript 的过程，其他颜色的条目表示浏览器内部系统的执行过程。

通过该图可以看出来，Parse HTML 任务在执行过程中会遇到一系列的子过程，比如在解析页面的过程中遇到了 JavaScript 脚本，那么就暂停解析过程去执行该脚本，等执行完成之后，再恢复解析过程。然后又遇到了样式表，这时候又开始解析样式表......直到整个任务执行完成。

需要说明的是，整个 Parse HTML 是一个完整的任务，在执行过程中的脚本解析、样式表解析都是该任务的子过程，**其下拉的长条就是执行过程中调用栈的信息**。

异步回调是指回调函数在主函数之外执行，一般有两种方式：

* 一是把异步函数做成一个任务，添加到信息队列尾部；
* 二是把异步函数添加到微任务队列中，这样就可以在当前任务的末尾处执行微任务了。

### XMLHttpRequest 运作机制

具体工作过程如下图所示：

![](https://tva1.sinaimg.cn/large/0081Kckwly1gm9f152f4wj30xq0giahj.jpg)



下面是一端利用 XMLHttpRequest 来请求数据的代码：

```js
function GetWebData(URL){    
  /**     
  * 1: 新建 XMLHttpRequest 请求对象     
  */    
  let xhr = new XMLHttpRequest()    
  /**     
  * 2: 注册相关事件回调处理函数     
  */    
  xhr.onreadystatechange = function () {        
    switch(xhr.readyState){          
      case 0: // 请求未初始化            
        console.log(" 请求未初始化 ")            
        break;          
      case 1://OPENED            
        console.log("OPENED")            
        break;          
      case 2://HEADERS_RECEIVED            
        console.log("HEADERS_RECEIVED")            
        break;          
      case 3://LOADING              
        console.log("LOADING")            
        break;          
      case 4://DONE            
        if(this.status == 200||this.status == 304){                
          console.log(this.responseText);                }            
        console.log("DONE")            
        break;        
    }    
  }    
  xhr.ontimeout = function(e) { 
    console.log('ontimeout') 
  }    
  xhr.onerror = function(e) { 
    console.log('onerror') 
  }    
  /**     
  * 3: 打开请求     
  */    
  xhr.open('Get', URL, true);// 创建一个 Get 请求, 采用异步
  /**     
  * 4: 配置参数     
  */    
  xhr.timeout = 3000 // 设置 xhr 请求的超时时间    
  xhr.responseType = "text" // 设置响应返回的数据格式    
  xhr.setRequestHeader("X_TEST","time.geekbang")    
  /**     
  * 5: 发送请求     
  */    
  xhr.send();
}
```

结合上述代码和流程图，分析这段代码的执行。

##### 第一步：创建 XMLHttpRequest 对象

当执行到`let xhr = new XMLHttpRequest()`后，JavaScript 会创建一个`XMLHttpRequest` 对象`xhr`，用来执行实际的网络请求操作。

##### 第二步：为 xhr 对象注册回调函数

因为网络请求比较耗时，所以要注册回调函数，这样后台任务执行完成之后就会通过调用回调函数来告诉其执行结果。`XMLHttpRequest `的回调函数主要有下面几种：

* `ontimeout`，用来监控超时请求，如果后台请求超时了，该函数会被调用；
* `onerror`，用来监控出错信息，如果后台请求出错了，该函数会被调用；
* `onreadystatechange`，用来监控后台请求过程中的状态，比如可以监控到 HTTP 头加载完成的消息、HTTP 响应体消息以及数据加载完成的消息等。

##### 第三步：配置基础的请求信息

注册好回调事件之后，接下来就需要配置基础的请求信息了，首先要通过 open 接口配置一些基础的请求信息，包括请求的地址、请求方法（是 get 还是 post）和请求方式（同步还是异步请求）。

然后通过 xhr 内部属性类配置一些其他可选的请求信息，可以参考文中示例代码，我们通过`xhr.timeout = 3000`来配置超时时间，也就是说如果请求超过 3000 毫秒还没有响应，那么这次请求就被判断为失败了。

还可以通过`xhr.responseType = "text"`来配置服务器返回的格式，将服务器返回的数据自动转换为自己想要的格式，如果将 responseType 的值设置为 json，那么系统会自动将服务器返回的数据转换为 JavaScript 对象格式。下面的图表是列出的一些返回类型的描述：

![截屏2021-01-02 16.21.00](https://tva1.sinaimg.cn/large/0081Kckwly1gm9fclw9ylj30yk0i4ti2.jpg)

如果还需要添加自己专用的请求头属性，可以通过 `xhr.setRequestHeader` 来添加。

##### 第四步：发起请求

一切准备就绪之后，就可以调用`xhr.send`来发起网络请求了。对照上面那张请求流程图，可以看到：**渲染进程会将请求发送给网络进程，然后网络进程负责资源的下载，等网络进程接收到数据之后，就会利用 IPC 来通知渲染进程；渲染进程接收到消息之后，会将xhr 的回调函数封装成任务并添加到消息队列中，等主线程循环系统执行到该任务的时候，就会根据相关的状态来调用对应的回调函数**。

如果网络请求出错了，就会执行` xhr.onerror`；如果超时了，就会执行 `xhr.ontimeout`；如果是正常的数据接收，就会执行 `onreadystatechange` 来反馈相应的状态。

这就是一个完整的 XMLHttpRequest 请求流程。

### XMLHttpRequest 使用过程中的问题

#### 1、跨域问题

比如在极客邦的官网使用 XMLHttpRequest 请求极客时间的页面内容，由于极客邦的官网是www.geekbang.org，极客时间的官网是time.geekbang.org，它们不是同一个源，所以就涉及到了跨域（在 A 站点中去访问不同源的 B 站点的内容）。默认情况下，跨域请求是不被允许的，如下面代码：

```js
GetWebData('www.geekbang.org');
```

可以在控制台测试下。首先通过浏览器打开www.geekbang.org，然后打开控制台，在控制台输入以上示例代码，再执行，会看到请求被 Block 了。

因为 www.geekbang.org 和 time.geekbang.com 不属于一个域，所以以上访问就属于跨域访问了，这次访问失败就是由于跨域问题导致的。

#### 2、HTTPS混合内容问题

HTTPS 混合内容是 HTTPS 页面中包含了不符合 HTTPS 安全要求的内容，比如包含了 HTTP 资源，通过 HTTP 加载的图像、视频、样式表、脚本等，都属于混合内容。

通常，如果 HTTPS 请求页面中使用混合内容，浏览器会针对 HTTPS 混合内容显示警告，用来向用户表明此 HTTPS 页面包含不安全的资源。比如打开站点https://www.iteye.com/groups ，可以通过控制台看到混合内容的警告，参考下图

![](https://tva1.sinaimg.cn/large/0081Kckwly1gm9fy4hmanj30yq0aigwu.jpg)

从上图可以看出，通过 HTML 文件加载的混合资源，虽然给出警告，但大部分类型还是能加载的。而使用 XMLHttpRequest 请求时，浏览器认为这种请求可能是攻击者发起的，会阻止此类危险的请求。比如我通过浏览器打开地址 https://www.iteye.com/groups，然后通过控制台，使用 XMLHttpRequest 来请求 http://img-ads.csdn.net/2018/201811150919211586.jpg ，这时候请求就会报错，出错信息如下图所示：

![](https://tva1.sinaimg.cn/large/0081Kckwly1gm9fy052mgj30yg0fytfo.jpg)






