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

## 宏任务和微任务

随着浏览器的应用领域越来越广泛，消息队列中这种粗时间颗粒度的任务已经不能胜任部分领域的需求，所以又出现了一种新的技术——微任务。**微任务可以在实时性和效率之间做一个有效的权衡**。从目前的情况来看，微任务已经被广泛地应用，基于微任务的技术有MutationObserver、Promise 以及以 Promise 为基础开发出来的很多其他的技术。

### 宏任务

页面中的大部分任务都是在主线程上执行的，这些任务包括了：

* 渲染事件（如解析 DOM、计算布局、绘制）；
* 用户交互事件（如鼠标点击、滚动页面、放大缩小等）；
* JavaScript 脚本执行事件；
* 网络请求完成、文件读写完成事件。

为了协调这些任务有条不紊地在主线程上执行，页面进程引入了**消息队列和事件循环机制**，渲染进程内部会维护多个消息队列，比如延迟执行队列和普通的消息队列。然后主线程采用一个 for 循环，不断地从这些任务队列中取出任务并执行任务。我们把这些消息队列中的任务称为**宏任务**。

消息队列中的任务是通过事件循环系统来执行的，这里我们可以看看在WHATWG 规范中是怎么定义事件循环机制的。

> WHATWG（*Web Hypertext Application Technology Working Group - Web 超文本应用技术工作组*）是为 Web 应用程序维护和开发 HTML 和 API 的组织。WHATWG 维护了 HTML，DOM 和 JavaScript 的规范。

由于规范需要支持语义上的完备性，所以通常写得都会比较啰嗦，这里大致总结了下WHATWG 规范定义的大致流程：

1、先从多个消息队列中选出一个最老的任务，这个任务称为 oldestTask；

2、然后循环系统记录任务开始执行的时间，并把这个 oldestTask 设置为当前正在执行的任务；

3、当任务执行完成之后，删除当前正在执行的任务，并从对应的消息队列中删除掉这个oldestTask；

4、最后统计执行完成的时长等信息。

以上就是消息队列中宏任务的执行过程。

宏任务可以满足我们大部分的日常需求，不过**如果有对时间精度要求较高的需求，宏任务就难以胜任了**，下面我们就来分析下为什么宏任务难以满足对时间精度要求较高的任务。

前面我们说过，页面的渲染事件、各种 IO 的完成事件、执行 JavaScript 脚本的事件、用户交互的事件等都随时有可能被添加到消息队列中，而且添加事件是由系统操作的，JavaScript 代码不能准确掌控任务要添加到队列中的位置，控制不了任务在消息队列中的位置，所以很难控制开始执行任务的时间。为了直观理解，可以看下面这段代码：

```html
<!DOCTYPE html>
<html>    
   <body>        
    <div id='demo'>            
      <ol>                
        <li>test</li>            
      </ol>        
    </div>    
  </body>    
  <script type="text/javascript">        
  function timerCallback2(){          
    console.log(2)        
  }        
  function timerCallback(){            
    console.log(1)            
    setTimeout(timerCallback2,0)        
  }        
  setTimeout(timerCallback,0)    
  </script>
</html>
```

在这段代码中，目的是想通过 setTimeout 来设置两个回调任务，并让它们按照前后顺序来执行，中间也不要再插入其他的任务，因为如果这两个任务的中间插入了其他的任务，就很有可能会影响到第二个定时器的执行时间了。

但实际情况是我们不能控制的，比如在调用 setTimeout 来设置回调任务的间隙，消息队列中就有可能被插入很多系统级的任务。你可以打开 Performance 工具，来记录下这段任务的执行过程：

![](https://tva1.sinaimg.cn/large/0081Kckwly1gmadabg4sjj30v209kdkg.jpg)

setTimeout 函数触发的回调函数都是宏任务，如图中，左右两个黄色块就是 setTimeout触发的两个定时器任务。上图中间浅红色区域，这里有很多一段一段的任务，这些是被渲染引擎插在两个定时器任务中间的任务。试想一下，如果中间被插入的任务执行时间过久的话，那么就会影响到后面任务的执行了。

所以说宏任务的时间粒度比较大，执行的时间间隔是不能精确控制的，对一些高实时性的需求就不太符合了，比如监听 DOM 变化的需求。

### 微任务

异步回调有两种方式：

* **第一种是把异步回调函数封装成一个宏任务，添加到消息队列尾部，当循环系统执行到该任务的时候执行回调函数**。前面介绍的 setTimeout 和XMLHttpRequest 的回调函数都是通过这种方式来实现的。
* **第二种方式的执行时机是在主函数执行结束之后、当前宏任务结束之前执行回调函数**，这通常都是以微任务形式体现的。

**微任务就是一个需要异步执行的函数，执行时机是在主函数执行结束之后、当前宏任务结束之前。**

我们知道当 JavaScript 执行一段脚本的时候，V8 会为其创建一个全局执行上下文，**在创建全局执行上下文的同时，V8 引擎也会在内部创建一个微任务队列**。顾名思义，这个微任务队列就是用来存放微任务的，因为在当前宏任务执行的过程中，有时候会产生多个微任务，这时候就需要使用这个微任务队列来保存这些微任务了。不过这个微任务队列是给 V8 引擎内部使用的，所以你是无法通过 JavaScript 直接访问的。

在现代浏览器里面，产生微任务有两种方式。

* 第一种方式是使用 MutationObserver 监控某个 DOM 节点，然后再通过 JavaScript 来修改这个节点，或者为这个节点添加、删除部分子节点，当 DOM 节点发生变化时，就会产生 DOM 变化记录的微任务。
* 第二种方式是使用 Promise，当调用 Promise.resolve() 或者 Promise.reject() 的时候，也会产生微任务。

通过 DOM 节点变化产生的微任务或者使用 Promise 产生的微任务都会被 JavaScript 引擎按照顺序保存到微任务队列中。

#### 微任务队列执行

通常情况下，在当前宏任务中的 JavaScript 快执行完成时，也就在 JavaScript 引擎准备退出全局执行上下文并清空调用栈的时候，JavaScript 引擎会检查全局执行上下文中的微任务队列，然后按照顺序执行队列中的微任务。**WHATWG 把执行微任务的时间点称为检查点**。当然除了在退出全局执行上下文式这个检查点之外，还有其他的检查点，不过不是太重要。

如果在执行微任务的过程中，产生了新的微任务，同样会将该微任务添加到微任务队列中，V8 引擎一直循环执行微任务队列中的任务，直到队列为空才算执行结束。也就是说在执行微任务过程中产生的新的微任务并不会推迟到下个宏任务中执行，而是在当前的宏任务中继续执行。

![截屏2021-01-03 12.11.23](https://tva1.sinaimg.cn/large/0081Kckwly1gmadr5furqj30xi0t8dx9.jpg)

该示意图是在执行一个 ParseHTML 的宏任务，在执行过程中，遇到了 JavaScript 脚本，那么就暂停解析流程，进入到 JavaScript 的执行环境。从图中可以看到，全局上下文中包含了微任务列表。

在 JavaScript 脚本的后续执行过程中，分别通过 Promise 和 removeChild 创建了两个微任务，并被添加到微任务列表中。接着 JavaScript 执行结束，准备退出全局执行上下文，这时候就到了检查点了，JavaScript 引擎会检查微任务列表，发现微任务列表中有微任务，那么接下来，依次执行这两个微任务。等微任务队列清空之后，就退出全局执行上下文。

以上就是微任务的工作流程，从上面分析我们可以得出如下几个结论：

* 微任务和宏任务是绑定的，每个宏任务在执行时，会创建自己的微任务队列。
* 微任务的执行时长会影响到当前宏任务的时长。比如一个宏任务在执行过程中，产生了100 个微任务，执行每个微任务的时间是 10 毫秒，那么执行这 100 个微任务的时间就是 1000 毫秒，也可以说这 100 个微任务让宏任务的执行时间延长了 1000 毫秒。所以你在写代码的时候一定要注意控制微任务的执行时长。
* 在一个宏任务中，分别创建一个用于回调的宏任务和微任务，无论什么情况下，微任务都早于宏任务执行。

### 监听 DOM 变化方法演变

现在知道了微任务是怎么工作的，那接下来我们再来看看微任务是如何应用在MutationObserver 中的。MutationObserver 是用来监听 DOM 变化的一套方法，而监听 DOM 变化一直是前端工程师一项非常核心的需求。

比如许多 Web 应用都利用 HTML 与 JavaScript 构建其自定义控件，与一些内置控件不同，这些控件不是固有的。为了与内置控件一起良好地工作，这些控件必须能够适应内容更改、响应事件和用户交互。因此，Web 应用需要监视 DOM 变化并及时地做出响应。

虽然监听 DOM 的需求是如此重要，不过早期页面并没有提供对监听的支持，所以那时要观察 DOM 是否变化，唯一能做的就是轮询检测，比如使用 setTimeout 或者 setInterval来定时检测 DOM 是否有改变。这种方式简单粗暴，但是会遇到两个问题：如果时间间隔设置过长，DOM 变化响应不够及时；反过来如果时间间隔设置过短，又会浪费很多无用的工作量去检查 DOM，会让页面变得低效。

直到 2000 年的时候引入了 Mutation Event，Mutation Event 采用了**观察者的设计模式**，当 DOM 有变动时就会立刻触发相应的事件，这种方式属于同步回调。

采用 Mutation Event 解决了实时性的问题，因为 DOM 一旦发生变化，就会立即调用JavaScript 接口。但也正是这种实时性造成了严重的性能问题，因为每次 DOM 变动，渲染引擎都会去调用 JavaScript，这样会产生较大的性能开销。比如利用 JavaScript 动态创建或动态修改 50 个节点内容，就会触发 50 次回调，而且每个回调函数都需要一定的执行时间，这里我们假设每次回调的执行时间是 4 毫秒，那么 50 次回调的执行时间就是 200毫秒，若此时浏览器正在执行一个动画效果，由于 Mutation Event 触发回调事件，就会导致动画的卡顿。

也正是因为使用 Mutation Event 会导致页面性能问题，所以 Mutation Event 被反对使用，并逐步从 Web 标准事件中删除了。

为了解决了 Mutation Event 由于同步调用 JavaScript 而造成的性能问题，从 DOM4 开始，推荐使用 MutationObserver 来代替 Mutation Event。MutationObserver API 可以用来监视 DOM 的变化，包括属性的变化、节点的增减、内容的变化等。

那么相比较 Mutation Event，MutationObserver 到底做了哪些改进呢？

首先，**MutationObserver 将响应函数改成异步调用，可以不用在每次 DOM 变化都触发异步调用，而是等多次 DOM 变化后，一次触发异步调用**，并且还会使用一个数据结构来记录这期间所有的 DOM 变化。这样即使频繁地操纵 DOM，也不会对性能造成太大的影响。

我们**通过异步调用和减少触发次数来缓解了性能问题**，那么如何保持消息通知的及时性呢？如果采用 setTimeout 创建宏任务来触发回调的话，那么实时性就会大打折扣，因为上面我们分析过，在两个任务之间，可能会被渲染进程插入其他的事件，从而影响到响应的实时性。

这时候，微任务就可以上场了，在每次 DOM 节点发生变化的时候，渲染引擎将变化记录封装成微任务，并将微任务添加进当前的微任务队列中。这样当执行到检查点的时候，V8引擎就会按照顺序执行微任务了。

综上所述， **MutationObserver 采用了“异步 + 微任务”的策略**。**通过异步操作解决了同步操作的性能问题；通过微任务解决了实时性的问题。**

## Promise

> 使用Promise，告别回调函数

### 出现的原因

在事件循环系统中，页面中任务都是执行在主线程之上的，相对于页面来说，主线程就是它整个的世界，所以在执行一项耗时的任务时，比如下载网络文件任务、获取摄像头等设备信息任务，这些任务都会放到页面主线程之外的进程或者线程中去执行，这样就避免了耗时任务“霸占”页面主线程的情况。你可以结合下图来看看这个处理过程：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gmpykhfml8j30vc0nq0zy.jpg" style="zoom:50%;" />

上图展示的是一个标准的**异步编程模型**，页面主线程发起了一个耗时的任务，并将任务交给另外一个进程去处理，这时页面主线程会继续执行消息队列中的任务。等该进程处理完这个任务后，会将该任务添加到渲染进程的消息队列中，并排队等待循环系统的处理。排队结束之后，循环系统会取出消息队列中的任务进行处理，并触发相关的回调操作。这就是页面编程的一大特点：**异步回调。**

**问题一**：页面编程异步回调：当多次回调后会导致**代码逻辑不连续，不线性**，不符合人的直觉。可以通过**封装异步代码**，让处理流程变得线性。如下图：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gmpxguyso6j30w60g8q6h.jpg" style="zoom:50%;" />

**问题二**：但当代码中嵌套了太多的回调函数就容易让自己陷入**回调地狱**。总结原因有以下两点：

* 第一是**嵌套调用**，下面的任务依赖上个任务的请求结果，并在上个任务的回调函数内部执行新的业务逻辑，这样当嵌套层次多了之后，代码的可读性就变得非常差了。
* 第二是**任务的不确定性**，执行每个任务都有两种可能的结果（成功或者失败），所以体现在代码中就需要对每个任务的执行结果做两次判断，这种对每个任务都要进行一次额外的错误处理的方式，明显增加了代码的混乱程度。

因此，为了解决上面的两个问题，即：一是消灭嵌套调用；二是合并多个任务的错误处理。Promise出现。

### Promise

##### 1、解决嵌套回调问题

产生嵌套函数的一个主要原因是在发起任务请求时会带上回调函数，这样当任务处理结束之后，下个任务就只能在回调函数中来处理了。

Promise 主要通过下面两步解决嵌套回调问题的。

首先，**Promise 实现了回调函数的延时绑定**。回调函数的延时绑定在代码上体现就是先创建 Promise 对象 p，通过 Promise 的构造函数 executor 来执行业务逻辑；创建好Promise 对象 p 之后，再使用 p.then 来设置回调函数。示范代码如下：

```js
// 创建 Promise 对象 p，并在 executor 函数中执行业务逻辑
function executor(resolve, reject){
  resolve(100);
}
let p = new Promise(executor);
// p延迟绑定回调函数 onResolve 
function onResolve(value){
  console.log(value);
}
p.then(onResolve);
```

其次，需要将回调函数 onResolve 的返回值穿透到最外层。因为我们会根据 onResolve函数的传入值来决定创建什么类型的 Promise 任务，创建好的 Promise 对象需要返回到最外层，这样就可以摆脱嵌套循环了。你可以先看下面的代码：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gmpxtbr2f5j30vk0oygtp.jpg" style="zoom:50%;" />

现在我们知道了 Promise 通过回调函数延迟绑定和回调函数返回值穿透的技术，解决了循环嵌套。

##### 2、处理异常错误问题

如下面代码所示：

```js
function executor(resolve, reject) {    
  let rand = Math.random();    
  console.log(1)    
  console.log(rand)    
  if (rand > 0.5)        
    resolve()    
  else        
    reject()
}
var p0 = new Promise(executor);
var p1 = p0.then((value) => {    
  console.log("succeed-1")    
  return new Promise(executor)
})
var p3 = p1.then((value) => {    
  console.log("succeed-2")    
  return new Promise(executor)
})
var p4 = p3.then((value) => {    
  console.log("succeed-3")    
  return new Promise(executor)
})
p4.catch((error) => {    
  console.log("error")
})
console.log(2)
```

这段代码有四个 Promise 对象：p0～p4。无论哪个对象里面抛出异常，都可以通过最后一个对象 p4.catch 来捕获异常，通过这种方式可以将所有 Promise 对象的错误合并到一个函数来处理，这样就解决了每个任务都需要单独处理异常的问题。

> 之所以可以使用最后一个对象来捕获所有异常，是因为 Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被 onReject 函数处理或 catch 语句捕获为止。具备了这样“冒泡”的特性后，就不需要在每个 Promise 对象中单独捕获异常了。

### Promise 与微任务

如下方代码所示：

```js
function executor(resolve, reject) {    
  resolve(100)
}
let demo = new Promise(executor)
function onResolve(value){    
  console.log(value)
}
demo.then(onResolve)
```

对于上面这段代码，我们需要重点关注下它的执行顺序。

首先执行 new Promise 时，Promise 的构造函数会被执行，不过由于 Promise 是 V8 引擎提供的，所以暂时看不到 Promise 构造函数的细节。

接下来，Promise 的构造函数会调用 Promise 的参数 executor 函数。然后在 executor中执行了 resolve，resolve 函数也是在 V8 内部实现的，那么 resolve 函数到底做了什么呢？我们知道，执行 resolve 函数，会触发 demo.then 设置的回调函数 onResolve，所以可以推测，resolve 函数内部调用了通过 demo.then 设置的 onResolve 函数。

不过这里需要注意一下，由于 Promise 采用了回调函数延迟绑定技术，所以在执行resolve 函数的时候，回调函数还没有绑定，那么只能推迟回调函数的执行。

接下来，模拟实现一个 Promise，我们会实现它的构造函数、resolve 方法以及 then 方法，以方便看清楚 Promise 的背后都发生了什么。这里我们就把这个对象称为 Bromise，下面就是 Bromise 的实现代码：

```js
function Bromise(executor) {    
  var onResolve_ = null    
  var onReject_ = null     
  // 模拟实现 resolve 和 then，暂不支持 rejcet    
  this.then = function (onResolve, onReject) {
    onResolve_ = onResolve    
  };    
  function resolve(value) {          
    //setTimeout(()=>{            
    onResolve_(value)           
    // },0)    
  }    
  executor(resolve, null);
}
```

观察上面这段代码，我们实现了自己的构造函数、resolve、then 方法。接下来我们使用Bromise 来实现我们的业务代码，实现后的代码如下所示

```js
function executor(resolve, reject) {    
  resolve(100)
}
// 将 Promise 改成我们自己的 Bromsie
let demo = new Bromise(executor)
function onResolve(value){
  console.log(value)
}
demo.then(onResolve)
```

执行这段代码，我们发现执行出错，输出的内容是：

```js
Uncaught TypeError: onResolve_ is not a function    
		at resolve (<anonymous>:10:13)    
		at executor (<anonymous>:17:5)    
		at new Bromise (<anonymous>:13:5)    
		at <anonymous>:19:12
```

之所以出现这个错误，是由于 Bromise 的延迟绑定导致的，在调用到 onResolve_ 函数的时候，Bromise.then 还没有执行，所以执行上述代码的时候，当然会报“onResolve_ isnot a function“的错误了。

也正是因为此，我们要改造 Bromise 中的 resolve 方法，让 resolve 延迟调用onResolve_。

要让 resolve 中的 onResolve_ 函数延后执行，可以在 resolve 函数里面加上一个定时器，让其延时执行 onResolve_ 函数，你可以参考下面改造后的代码：

```js
function resolve(value) {          
    setTimeout(()=>{            
    	onResolve_(value)           
    },0)  
```

上面采用了定时器来推迟 onResolve 的执行，不过使用定时器的效率并不是太高，好在我们有微任务，所以 Promise 又把这个定时器改造成了微任务了，这样既可以让onResolve_ 延时被调用，又提升了代码的执行效率。这就是 Promise 中使用微任务的原由了。

> 1. Promise 中为什么要引入微任务？
> 2. Promise 中是如何实现回调函数返回值穿透的？
> 3. Promise 出错后，是怎么通过“冒泡”传递给最后那个捕获异常的函数？

## async/await

> 使用同步的方式去写异步代码

上文讲到使用Promise实现回调操作，能够很好地解决回调地狱的问题，但是这种方式充满了 Promise 的 then() 方法，如果处理流程比较复杂的话，那么整段代码将充斥着 then，语义化不明显，代码不能很好地表示执行流程。如下面代码所示：

```js
fetch('https://www.geekbang.org')
.then((response) => {
  console.log(response);
  return fetch('https://www.geekbang.org/test');
}).then((response) => {
  console.log(response);
})catch((error) => {
  console.log(error);
})
```

上面代码使用 fetch 来实现，fetch 被定义在window 对象中，可以用它来发起对远程资源的请求，该方法返回的是一个 Promise 对象。

从这段 Promise 代码可以看出来，使用 promise.then 也是相当复杂，虽然整个请求流程已经线性化了，但是代码里面包含了大量的 then 函数，使得代码依然不是太容易阅读。基于这个原因，**ES7 引入了 async/await，这是 JavaScript 异步编程的一个重大改进，提供了在不阻塞主线程的情况下使用同步代码实现异步访问资源的能力，并且使得代码逻辑更加清晰**。可以参考下面这段代码：

```js
async function foo(){
  try{
    let response1 = await fetch('https://www.geekbang.org');
    console.log('response1');
    console.log(response1);
    let response2 = await fetch('https://www.geekbang.org/test');
    console.log('response2');
    console.log(response2);
  }catch(err){
    console.log(err);
  }
}
foo();
```

通过上面代码，会发现**整个异步处理的逻辑都是使用同步代码的方式来实现**的，而且还**支持 try catch 来捕获异常**，这就是完全在写同步代码，所以是非常符合人的线性思维的。

接下来介绍JavaScript 引擎是如何实现 async/await 的。首先介绍生成器（Generator）是如何工作的，接着讲解 Generator 的底层实现机制——协程（Coroutine）；又因为 async/await 使用了 Generator 和 Promise 两种技术，所以紧接着我们就通过 Generator 和 Promise 来分析 async/await 到底是如何以同步的方式来编写异步代码的。

### 生成器 VS 协程

##### 生成器

**生成器函数是一个带星号函数，而且是可以暂停执行和恢复执行的**。我们可以看下面这段代码：

```js
function* genDemo() {    
  console.log(" 开始执行第一段 ")    
  yield 'generator 1'    
  console.log(" 开始执行第二段 ")    
  yield 'generator 2'    
  console.log(" 开始执行第三段 ")    
  yield 'generator 3'    
  console.log(" 执行结束 ")    
  return 'generator 3'
}
console.log('main 0')
let gen = genDemo()
console.log(gen.next().value)
console.log('main 1')
console.log(gen.next().value)
console.log('main 2')
console.log(gen.next().value)
console.log('main 3')
console.log(gen.next().value)
console.log('main 4')
// 执行结果
// main 0
// 开始执行第一段 
// generator 1
// main 1
// 开始执行第二段 
// generator 2
// main 2
// 开始执行第三段 
// generator 3
// main 3
// 执行结束 
// generator 3
// main 4
```

执行上面的代码，会发现函数 genDemo 并不是一次执行完的，全局代码和 genDemo 函数交替执行。其实这就是生成器函数的特性，可以暂停执行，也可以恢复执行。

下面我们就来看看生成器函数的具体使用方式：

1. 在生成器函数内部执行一段代码，如果遇到 **yield 关键字**，那么 JavaScript 引擎将返回关键字后面的内容给外部，并暂停该函数的执行。
2. 外部函数可以通过 **next 方法**恢复函数的执行。

那JavaScript 引擎 V8 是如何实现一个函数的暂停和恢复的呢？首先要了解协程的概念。

##### 协程

**协程是一种比线程更加轻量级的存在**。你可以把协程看成是跑在线程上的任务，**一个线程上可以存在多个协程，但是在线程上同时只能执行一个协程**，比如当前执行的是 A 协程，要启动 B 协程，那么 A 协程就需要将主线程的控制权交给 B 协程，这就体现在 A 协程暂停执行，B 协程恢复执行；同样，也可以从 B 协程中启动 A 协程。通常，如果从 A 协程启动 B 协程，我们就把 A 协程称为B 协程的父协程。正如一个进程可以拥有多个线程一样，一个线程也可以拥有多个协程。最重要的是，**协程不是被操作系统内核所管理，而完全是由程序所控制（也就是在用户态执行）**。这样带来的好处就是性能得到了很大的提升，不会像线程切换那样消耗资源。

为了让你更好地理解协程是怎么执行的，结合上面那段代码的执行过程，画出了下面的“协程执行流程图”。

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gmqi4890g7j30zy0fcdlt.jpg" style="zoom:50%;" />

从图中可以看出来**协程的四点规则**：

1. 通过调用生成器函数 genDemo 来创建一个协程 gen，创建之后，gen 协程并没有立即执行。
2. 要让 gen 协程执行，需要通过调用 gen.next。
3. 当协程正在执行的时候，可以通过 yield 关键字来暂停 gen 协程的执行，并返回主要信息给父协程。
4. 如果协程在执行期间，遇到了 return 关键字，那么 JavaScript 引擎会结束当前协程，并将 return 后面的内容返回给父协程。

不过，对于上面这段代码，你可能又有这样疑问：父协程有自己的调用栈，gen 协程时也有自己的调用栈，当 gen 协程通过 yield 把控制权交给父协程时，V8 是如何切换到父协程的调用栈？当父协程通过 gen.next 恢复 gen 协程时，又是如何切换 gen 协程的调用栈？

要搞清楚上面的问题，需要关注以下两点内容。

第一点：gen 协程和父协程是在主线程上交互执行的，并不是并发执行的，它们之前的切换是通过 yield 和 gen.next 来配合完成的。

第二点：当在 gen 协程中调用了 yield 方法时，JavaScript 引擎会保存 gen 协程当前的调用栈信息，并恢复父协程的调用栈信息。同样，当在父协程中执行 gen.next 时，JavaScript 引擎会保存父协程的调用栈信息，并恢复 gen 协程的调用栈信息。

为了直观理解父协程和 gen 协程是如何切换调用栈的，你可以参考下图：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gmqiq6g5u6j30x00sa45e.jpg" style="zoom:50%;" />

其实在 JavaScript 中，生成器就是协程的一种实现方式，那么接下来，我们使用生成器和Promise 来改造开头的那段 Promise 代码。改造后的代码如下所示：

```js
//foo 函数
function* foo() {    
  let response1 = yield fetch('https://www.geekbang.org')    
  console.log('response1')    
  console.log(response1)    
  let response2 = yield fetch('https://www.geekbang.org/test')    
  console.log('response2')    
  console.log(response2)
}
// 执行 foo 函数的代码
let gen = foo()
function getGenPromise(gen) {    
  return gen.next().value
}
getGenPromise(gen).then((response) => {
  console.log('response1')    
  console.log(response)    
  return getGenPromise(gen)
}).then((response) => {    
  console.log('response2')    
  console.log(response)
})
```

从图中可以看到，foo 函数是一个生成器函数，在 foo 函数里面实现了用同步代码形式来实现异步操作；但是在 foo 函数外部，我们还需要写一段执行 foo 函数的代码，如上述代码的后半部分所示，那下面我们就来分析下这段代码是如何工作的。

首先执行的是let gen = foo()，创建了 gen 协程。

然后在父协程中通过执行 gen.next 把主线程的控制权交给 gen 协程。

gen 协程获取到主线程的控制权后，就调用 fetch 函数创建了一个 Promise 对象response1，然后通过 yield 暂停 gen 协程的执行，并将 response1 返回给父协程。

父协程恢复执行后，调用 response1.then 方法等待请求结果。

等通过 fetch 发起的请求完成之后，会调用 then 中的回调函数，then 中的回调函数拿到结果之后，通过调用 gen.next 放弃主线程的控制权，将控制权交 gen 协程继续执行下个请求。

以上就是协程和 Promise 相互配合执行的一个大致流程。不过通常，我们把执行生成器的代码封装成一个函数，并把这个执行生成器代码的函数称为执行器（可参考著名的 co 框架），如下面这种方式：

```js
function* foo() {    
  let response1 = yield fetch('https://www.geekbang.org')    
  console.log('response1')    
  console.log(response1)    
  let response2 = yield fetch('https://www.geekbang.org/test')    
  console.log('response2')    console.log(response2)
}
co(foo());
```

通过使用生成器配合执行器，就能实现使用同步的方式写出异步代码了，这样也大大加强了代码的可读性。

### async/await

ES7 中引入了 async/await，这种方式能够彻底告别执行器和生成器，实现更加直观简洁的代码。其实 async/await 技术背后的秘密就是 Promise 和生成器应用，往低层说就是微任务和协程应用。

#### 1. async

根据 MDN 定义，**async 是一个通过异步执行并隐式返回 Promise 作为结果的函数**。

我们先来看看是如何隐式返回 Promise的，你可以参考下面的代码：

```js
async function foo() {    
  return 2
}
console.log(foo())  // Promise {<resolved>: 2}
```

执行这段代码，我们可以看到调用 async 声明的 foo 函数返回了一个 Promise 对象，状态是 resolved。

#### 2. await

我们知道了 async 函数返回的是一个 Promise 对象，那下面我们再结合下面这段代码来看看 await 到底是什么。

```js
async function foo() {    
  console.log(1)    
  let a = await 100    
  console.log(a)    
  console.log(2)
}
console.log(0)
foo()
console.log(3)
```

观察上面这段代码，站在协程的视角来看看这段代码的整体执行流程图：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gmqjifuxikj30zq0fqafk.jpg" style="zoom:50%;" />

结合上图，我们来一起分析下 async/await 的执行流程。

首先，执行console.log(0)这个语句，打印出来 0。

紧接着就是执行 foo 函数，由于 foo 函数是被 async 标记过的，所以当进入该函数的时候，JavaScript 引擎会保存当前的调用栈等信息，然后执行 foo 函数中的console.log(1)语句，并打印出 1。

接下来就执行到 foo 函数中的await 100这个语句了，这里是我们分析的重点，因为在执行await 100这个语句时，JavaScript 引擎在背后为我们默默做了太多的事情，那么下面我们就把这个语句拆开，来看看 JavaScript 到底都做了哪些事情。

当执行到await 100时，会默认创建一个 Promise 对象，代码如下所示:

```js
let promise_ = new Promise((resolve,reject){  
   resolve(100)
})
```

在这个 promise_ 对象创建的过程中，我们可以看到在 executor 函数中调用了 resolve 函数，JavaScript 引擎会将该任务提交给微任务队列。

然后 JavaScript 引擎会暂停当前协程的执行，将主线程的控制权转交给父协程执行，同时会将 promise_ 对象返回给父协程。

主线程的控制权已经交给父协程了，这时候父协程要做的一件事是调用 `promise_`.then 来监控 promise 状态的改变。

接下来继续执行父协程的流程，这里我们执行console.log(3)，并打印出来 3。随后父协程将执行结束，在结束之前，会进入微任务的检查点，然后执行微任务队列，微任务队列中有resolve(100)的任务等待执行，执行到这里的时候，会触发 promise_.then 中的回调函数，如下所示：

```js
promise_.then((value)=>{   
  // 回调函数被激活后  
  // 将主线程控制权交给 foo 协程，并将 vaule 值传给协程
})
```

该回调函数被激活以后，会将主线程的控制权交给 foo 函数的协程，并同时将 value 值传给该协程。

foo 协程激活之后，会把刚才的 value 值赋给了变量 a，然后 foo 协程继续执行后续语句，执行完成之后，将控制权归还给父协程。

以上就是 await/async 的执行流程。正是因为 async 和 await 在背后为我们做了大量的工作，所以我们才能用同步的方式写出异步代码来。



