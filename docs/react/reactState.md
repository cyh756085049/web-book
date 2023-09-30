**React 是有多种模式的，主要包括 `legacy` 模式、 `blocking` 模式和 `concurrent` 模式。**基本平时用的都是 `legacy` 模式下的 `React`， `blocking` 可以视为 `concurrent` 的优雅降级版本和过渡版本，`React` 最终是以 `concurrent` 模式作为默认版本，对于 `concurrent` 模式下，会采用不同 `State` 更新逻辑。在 `Reactv18` 版本，`concurrent` 作为一个稳定的功能出现。

### state 到底是同步还是异步的？

## 类组件相关问题

### 类组件中 `setState` 的用法？

`React` 项目中 `UI` 的改变来源于 `state` 改变，类组件中 `setState` 是更新组件，渲染视图的主要方式。

```js
setState(obj,callback)
```

- 第一个参数：当 `obj` 为一个对象，则为即将合并的 `state` ；如果 `obj` 是一个函数，那么当前组件的 `state` 和 `props` 将作为参数，返回值用于合并新的 `state`。
- 第二个参数 `callback` ：`callback` 为一个函数，函数执行上下文中**可以获取当前 `setState` 更新后的最新 `state` 的值**，可以作为依赖 `state` 变化的副作用函数，可以用来做一些基于 `DOM` 的操作。

### `React setState` 触发一次，`React` 底层源码层面做了什么？

```js
/* 第一个参数为function类型 */
this.setState((state,props)=>{
    return { number:1 } 
})
/* 第一个参数为object类型 */
this.setState({ number:1 },()=>{
    console.log(this.state.number) //获取最新的number
})
```

假如一次事件中触发一次如上 `setState` ，在 `React` 底层主要做了以下一些事情：

- 首先，`setState` 会产生当前更新的优先级（老版本用 `expirationTime` ，新版本用 `lane` ）。
- 接下来 `React` 会从 `fiber Root` 根部 `fiber` 向下调和子节点，调和阶段将对比发生更新的地方，更新对比 `expirationTime` ，找到发生更新的组件，合并 `state`，然后触发 `render` 函数，得到新的 `UI` 视图层，完成 `render` 阶段。
- 接下来到 `commit` 阶段，在 `commit` 阶段替换真实 `DOM` ，完成此次更新流程。
- 此时仍然在 `commit` 阶段，会执行 `setState` 中 `callback` 函数， 如上的`()=>{ console.log(this.state.number) }`，到此为止完成了一次 `setState` 全过程。

**总结：更新优先级 -> 更新调度，调和 `fiber` 树  -> 合并 `state`,  `render` 阶段 `render` 函数执行  ->  `commit` 阶段真实 `DOM` 替换 -> `setState` 回调函数执行 `callback` 。**

### 类组件如何限制 `state` 更新视图?

- `pureComponent` 纯组件可以对 `state` 和 `props` 进行浅比较，如果没有发生变化，那么组件不更新。
- `shouldComponentUpdate` 生命周期可以通过判断前后 `state` 变化来决定组件需不需要更新，需要更新返回 `true`，否则返回 `false`。

### 类组件 `state` 的更新机制（源码层面）？

对于类组件，类组件初始化过程中绑定了负责更新的`updater`对象，对于调用 `setState` 方法，实际上是 `React` 底层调用 `updater` 对象上的 `enqueueSetState` 方法。

因为要弄明白 `state` 更新机制，所以接下来要从两个方向分析。

- 一是揭秘 `enqueueSetState` 到底做了些什么？
- 二是 `React` 底层是如何进行批量更新的？

```js
// react-reconciler/src/ReactFiberClassComponent.js
// 旧版源码
enqueueSetState(){
     /* 每一次调用`setState`，react 都会创建一个 update 里面保存了 */
     const update = createUpdate(expirationTime, suspenseConfig);
     /* callback 可以理解为 setState 回调函数，第二个参数 */
     callback && (update.callback = callback) 
     /* enqueueUpdate 把当前的update 传入当前fiber，待更新队列中 */
     enqueueUpdate(fiber, update); 
     /* 开始调度更新 */
     scheduleUpdateOnFiber(fiber, expirationTime);
}

// 新版源码
enqueueSetState(inst: any, payload: any, callback) {
  // 获取当前fiber实例
    const fiber = getInstance(inst);
  // 更新优先级
    const lane = requestUpdateLane(fiber);
  
  // 创建一个 update 
    const update = createUpdate(lane);
    update.payload = payload;
    if (callback !== undefined && callback !== null) {
      ...
      // callback 可以理解为 setState 回调函数
      update.callback = callback;
    }
   // enqueueUpdate 把当前的update 传入当前fiber，待更新队列中
    const root = enqueueUpdate(fiber, update, lane);
    if (root !== null) {
      // 开始调度更新
      scheduleUpdateOnFiber(root, fiber, lane);
      entangleTransitions(root, fiber, lane);
    }
    ...
  },
```

**`enqueueSetState`** 作用实际很简单，就是创建一个 `update` ，然后放入当前 `fiber` 对象的待更新队列中，最后开启调度更新，进入更新流程。

#### `React` 的 `batchUpdate` 批量更新是什么时候加上去的呢？

这就要提前聊一下事件系统了。正常 **state 更新**、**UI 交互**，都离不开用户的事件，比如点击事件，表单输入等，`React` 是采用事件合成的形式，每一个事件都是由 `React` 事件系统统一调度的，那么 `State` 批量更新正是和事件系统息息相关的。

```js
// react-dom/src/events/DOMLegacyEventPluginSystem.js
/* 在legacy模式下，所有的事件都将经过此函数统一处理 */
function dispatchEventForLegacyPluginEventSystem(){
    // handleTopLevel 事件处理函数
    batchedEventUpdates(handleTopLevel, bookKeeping);
}

// react-dom/src/events/ReactDOMUpdateBatching.js
function batchedEventUpdates(fn,a){
    /* 开启批量更新  */
   isBatchingEventUpdates = true;
  try {
    /* 这里执行了的事件处理函数， 比如在一次点击事件中触发setState,那么它将在这个函数内执行 */
    return batchedEventUpdatesImpl(fn, a, b);
  } finally {
    /* try 里面 return 不会影响 finally 执行  */
    /* 完成一次事件，批量更新  */
    isBatchingEventUpdates = false;
  }
}
```

如上可以分析出流程，在 `React` 事件执行之前通过 `isBatchingEventUpdates=true` 打开开关，开启事件批量更新，当该事件结束，再通过 `isBatchingEventUpdates = false;` 关闭开关，然后在 `scheduleUpdateOnFiber` 中根据这个开关来确定是否进行批量更新。

如下示例代码：

```js
export default class index extends React.Component{
    state = { number:0 }
    handleClick= () => {
          this.setState({ number:this.state.number + 1 },()=>{   console.log( 'callback1', this.state.number)  })
          console.log(this.state.number)
          this.setState({ number:this.state.number + 1 },()=>{   console.log( 'callback2', this.state.number)  })
          console.log(this.state.number)
          this.setState({ number:this.state.number + 1 },()=>{   console.log( 'callback3', this.state.number)  })
          console.log(this.state.number)
    }
    render(){
        return <div>
            { this.state.number }
            <button onClick={ this.handleClick }  >number++</button>
        </div>
    }
} 

```

点击按钮打印结果：**0, 0, 0, callback1 1 ,callback2 1 ,callback3 1**

如上代码，在整个 `React` 上下文执行栈中会变成这样：

![执行栈](https://p.ipic.vip/wzou9q.jpg)

那么，为什么异步操作里面的批量更新规则会被打破呢？比如用 `promise` 或者 `setTimeout` 在 `handleClick` 中这么写：

```js
handleClick= () => {
  setTimeout(()=>{
      this.setState({ number:this.state.number + 1 },()=>{   console.log( 'callback1', this.state.number)  })
      console.log(this.state.number)
      this.setState({ number:this.state.number + 1 },()=>{  console.log( 'callback2', this.state.number)  })
      console.log(this.state.number)
      this.setState({ number:this.state.number + 1 },()=>{ console.log( 'callback3', this.state.number)  })
      console.log(this.state.number)
  })
}
```

点击按钮打印结果： **callback1 1 , 1, callback2 2 , 2,callback3 3 , 3**

那么在整个 `React` 上下文执行栈中会变成如下这样：

![异步上下文执行栈](https://p.ipic.vip/7bktxs.jpg)

**所以批量更新规则被打破**。

**那么，如何在如上异步环境下，继续开启批量更新模式呢？**

`React-Dom` 中提供了批量更新方法 `unstable_batchedUpdates`，可以去手动批量更新，可以将上述 `setTimeout` 里面的内容做如下修改:

```js
import ReactDOM from 'react-dom'
const { unstable_batchedUpdates } = ReactDOM

handleClick= () => {
  setTimeout(()=>{
      unstable_batchedUpdates(()=>{
          this.setState({ number:this.state.number + 1 })
          console.log(this.state.number)
          this.setState({ number:this.state.number + 1})
          console.log(this.state.number)
          this.setState({ number:this.state.number + 1 })
          console.log(this.state.number) 
      })
  })
}
```

点击按钮打印结果： **0 , 0 , 0 , callback1 1 , callback2 1 ,callback3 1**

在实际工作中，`unstable_batchedUpdates` 可以用于 `Ajax` 数据交互之后，合并多次 `setState`，或者是多次 `useState` 。原因很简单，所有的数据交互都是在异步环境下，如果没有批量更新处理，一次数据交互多次改变 `state` 会促使视图多次渲染。

### 如何提升更新优先级呢？

`React-dom` 提供了 `flushSync` ，`flushSync` 可以将回调函数中的更新任务，放在一个较高的优先级中。`React` 设定了很多不同优先级的更新任务。如果一次更新任务在 `flushSync` 回调函数内部，那么将获得一个较高优先级的更新。

接下来，将上述 `handleClick` 改版如下样子：

```js
handerClick=()=>{
    setTimeout(()=>{
        this.setState({ number: 1  })
    })
    this.setState({ number: 2  })
    ReactDOM.flushSync(()=>{
        this.setState({ number: 3  })
    })
    this.setState({ number: 4  })
}
render(){
   console.log(this.state.number)
   return ...
}
```

打印结果：**3 4 1**，相信不难理解为什么这么打印了。

- 首先 `flushSync` `this.setState({ number: 3 })`设定了一个高优先级的更新，所以 2 和 3 被批量更新到 3 ，所以 3 先被打印。
- 更新为 4。
- 最后更新 `setTimeout` 中的 `number = 1`。

**`flushSync`补充说明**：`flushSync` 在同步条件下，会合并之前的 `setState | useState`，可以理解成，如果发现了 `flushSync` ，就会先执行更新，如果之前有未更新的 `setState ｜ useState` ，就会一起合并了，所以就解释了如上，2 和 3 被批量更新到 3 ，所以 3 先被打印。

综上所述， `React` 同一级别**更新优先级**关系是:

**`flushSync` 中的 `setState` > 正常执行上下文中 `setState` > `setTimeout` ，`Promise` 中的 `setState`。**

## 函数组件相关问题

`React-hooks` 正式发布以后， `useState` 可以使函数组件像类组件一样拥有 `state`，也就说明函数组件可以通过 `useState` 改变 UI 视图。

### 函数组件 `useState` 的基本用法？

```js
[ state , dispatch ] = useState(initData)
```

-  `state`，目的提供给 `UI` ，作为渲染视图的数据源。
-  `dispatch` 改变 `state` 的函数，可以理解为推动函数组件渲染的渲染函数。
-  `initData` 有两种情况，第一种情况是非函数，将作为 `state` 初始化的值。 第二种情况是函数，函数的返回值作为 `useState` 初始化的值。

**`initData` 为非函数的情况:**

```js
/* 此时将把 0 作为初使值 */
const [ number , setNumber ] = React.useState(0);
```

**`initData` 为函数的情况:**

```js
 const [ number , setNumber ] = React.useState(()=>{
       /*  props 中 a = 1 state 为 0-1 随机数 ， a = 2 state 为 1 -10随机数 ， 否则，state 为 1 - 100 随机数   */
       if(props.a === 1) return Math.random() 
       if(props.a === 2) return Math.ceil(Math.random() * 10 )
       return Math.ceil(Math.random() * 100 ) 
    })
```

对于 `dispatch` 的参数, 也有两种情况：

- 第一种非函数情况，此时将作为新的值，赋予给 `state`，作为下一次渲染使用;
- 第二种是函数的情况，如果 `dispatch` 的参数为一个函数，这里可以称它为 `reducer`，`reducer` 参数，是上一次返回最新的 `state`，返回值作为新的 `state`。

**dispatch 参数是一个非函数值情况：**

```js
const [ number , setNumber ] = React.useState(0)
/* 一个点击事件 */
const handleClick=()=>{
   setNumber(1);
}
```

**dispatch 参数是一个函数情况：**

```js
const [ number , setNumber ] = React.useState(0)
const handleClick=()=>{
   setNumber((state)=> state + 1)  // state - > 0 + 1 = 1
   setNumber(8)  // state - > 8
   setNumber((state)=> state + 1)  // state - > 8 + 1 = 9
}
```

### 函数组件如何监听 state 变化？

类组件 `setState` 中，有第二个参数 `callback` 或者是生命周期 `componentDidUpdate` 可以检测监听到 `state` 改变或是组件更新。

那么在函数组件中，就需要通过 `useEffect` 来监听 `state` 变化，通常可以把 `state` 作为依赖项传入 `useEffect` 第二个参数 `deps` ，但是注意 `useEffect` 初始化会默认执行一次。

```js
export default function Index(props){
    const [ number , setNumber ] = React.useState(0)
    /* 监听 number 变化 */
    React.useEffect(()=>{
        console.log('监听number变化，此时的number是:  ' + number )
    },[ number ])
    const handerClick = ()=>{
        /** 高优先级更新 **/
        ReactDOM.flushSync(()=>{
            setNumber(2) 
        })
        /* 批量更新 */
        setNumber(1) 
        /* 滞后更新 ，批量更新规则被打破 */
        setTimeout(()=>{
            setNumber(3) 
        })
       
    }
    console.log(number)
    return <div>
        <span> { number }</span>
        <button onClick={ handerClick }  >number++</button>
    </div>
}
```

点击按钮之后打印结果：

```js
2
监听number变化，此时的number是: 2
1
监听number变化，此时的number是: 1
3
监听number变化，此时的number是: 3
```

### 函数组件 `dispatch` 的更新特点？

批量更新和提升更新优先级 `flushSync` 的方式，在函数组件中，`dispatch` 更新效果也一样的，但是 `useState` 有一点值得注意，就是当调用改变 `state` 的函数 `dispatch`，在本次函数执行上下文中，是获取不到最新的 `state` 值的，把上述 `demo` 如下这么改：

```js
const [ number , setNumber ] = React.useState(0)
const handleClick = ()=>{
    ReactDOM.flushSync(()=>{
        setNumber(2) 
        console.log(number) 
    })
    setNumber(1) 
    console.log(number)
    setTimeout(()=>{
        setNumber(3) 
        console.log(number)
    })   
}
```

**打印结果： 0 0 0**

原因很简单，函数组件更新就是函数的执行，在函数一次执行过程中，函数内部所有变量重新声明，所以改变的 `state` ，只有在下一次函数组件执行时才会被更新。所以在如上同一个函数执行上下文中，`number` 一直为0，无论怎么打印，都拿不到最新的 `state` 。

### 函数组件 `useState` 的注意事项？

在使用 `useState` 的 `dispatchAction` 更新 `state` 的时候，记得不要传入相同的 `state`，这样会使视图不更新。比如下面这么写：

```js
export default function Index(){
    const [ state  , dispatchState ] = useState({ name:'alien' })
    const  handleClick = ()=>{ // 点击按钮，视图没有更新。
        state.name = 'Alien'
        dispatchState(state) // 直接改变 `state`，在内存中指向的地址相同。
    }
    return <div>
         <span> { state.name }</span>
        <button onClick={ handleClick }  >changeName++</button>
    </div>
}

```

如上例子中，当点击按钮后，发现视图没有改变，主要原因是：

在 `useState` 的 `dispatchAction` 处理逻辑中，会浅比较两次 `state` ，发现 `state` 相同，不会开启更新调度任务； 示例中两次 `state` 指向了相同的内存空间，所以默认为 `state` 相等，就不会发生视图更新了。

**解决方案**： 把上述的 `dispatchState` 改成 `dispatchState({...state})` 解决了问题，浅拷贝了对象，重新申请了一个内存空间。

### `useState` 的原理，`state` 更新机制？

比如一段代码中这么写：

```js
const [ number,setNumber ] = React.useState(0)  
```

`setNumber` 本质就是 `dispatchAction` 。首先需要看一下执行 `useState(0)` 本质上做了些什么？

```js
// react-reconciler/src/ReactFiberHooks.js
function mountState(initialState){
     const hook = mountWorkInProgressHook(); // 每一个 hooks 初始化都会执行, 用于保存当前 hooks 的信息
    if (typeof initialState === 'function') {initialState = initialState() } // 如果 useState 第一个参数为函数，执行函数得到初始化state
     hook.memoizedState = hook.baseState = initialState;
    const queue = (hook.queue = { ... }); // 负责记录更新的各种状态。
    const dispatch = (queue.dispatch = (dispatchAction.bind(  null,currentlyRenderingFiber,queue, ))) // dispatchAction 为更新调度的主要函数 
    return [hook.memoizedState, dispatch];
}
```

- 上面的 `state` 会被当前 `hooks` 的 `memoizedState` 保存下来，每一个 `useState` 都会创建一个 `queue` 里面保存了更新的信息。
- 每一个 `useState` 都会创建一个更新函数，比如如上的 `setNumber` 本质上就是 `dispatchAction`，那么值得注意一点是，当前的 `fiber` 被 `bind` 绑定了固定的参数传入 `dispatchAction` 和 `queue` ，所以当用户触发 `setNumber` 的时候，能够直观反映出来自哪个 `fiber` 的更新。
- 最后把 `memoizedState dispatch` 返回给开发者使用。

接下来重点研究一下 `dispatchAction` ，底层是怎么处理更新逻辑的。

```js
function dispatchAction(fiber, queue, action){
    /* 第一步：创建一个 update */
    const update = { ... }
    const pending = queue.pending;
    if (pending === null) {  /* 第一个待更新任务 */
        update.next = update;
    } else {  /* 已经有待更新任务 */
       update.next = pending.next;
       pending.next = update;
    }
    if( fiber === currentlyRenderingFiber ){
        /* 说明当前fiber正在发生调和渲染更新，那么不需要更新 */
    }else{
       if(fiber.expirationTime === NoWork && (alternate === null || alternate.expirationTime === NoWork)){
            const lastRenderedReducer = queue.lastRenderedReducer;
            const currentState = queue.lastRenderedState;                 /* 上一次的state */
            const eagerState = lastRenderedReducer(currentState, action); /* 这一次新的state */
            if (is(eagerState, currentState)) {                           /* 如果每一个都改变相同的state，那么组件不更新 */
               return 
            }
       }
       scheduleUpdateOnFiber(fiber, expirationTime);    /* 发起调度更新 */
    }
}
```

当每一次改变 `state` ，底层会做这些事：

- 首先用户每一次调用 `dispatchAction`（比如如上触发 `setNumber` ）都会先创建一个 `update` ，然后把它放入待更新 `pending` 队列中。
- 然后判断如果当前的 `fiber` 正在更新，那么也就不需要再更新了。
- 反之，说明当前 `fiber` 没有更新任务，那么会拿出上一次 `state` 和 这一次 `state` 进行对比，如果相同，那么直接退出更新。如果不相同，那么发起更新调度任务。**这就解释了，为什么函数组件 `useState` 改变相同的值，组件不更新了。**

接下来就是更新的环节，下面模拟一个更新场景。

```js
export default  function Index(){
    const [ number , setNumber ] = useState(0)
    const handleClick=()=>{
        setNumber(num=> num + 1 ) // num = 1
        setNumber(num=> num + 2 ) // num = 3 
        setNumber(num=> num + 3 ) // num = 6
    }
    return <div>
        <button onClick={() => handleClick() } >点击 { number } </button>
    </div>
}
```

如上当点击一次按钮，触发了三次 `setNumber`，等于触发了三次 `dispatchAction` ，那么这三次 `update` 会在当前 `hooks` 的 `pending` 队列中，然后**根据事件批量更新的概念，会统一合成一次更新**。接下来就是组件渲染，那么就到了再一次执行 `useState`，此时走的是更新流程。那么试想一下点击 `handleClick` 最后 `state` 被更新成 6 ，那么在更新逻辑中 `useState` 内部要做的事，就是**得到最新的 `state` 。**

```js
function updateReducer(){
    // 第一步把待更新的pending队列取出来。合并到 baseQueue
    const first = baseQueue.next;
    let update = first;
   do {
        /* 得到新的 state */
        newState = reducer(newState, action);
    } while (update !== null && update !== first);
     hook.memoizedState = newState;
     return [hook.memoizedState, dispatch];
}
```

当再次执行 `useState` 的时候，会触发更新 `hooks` 逻辑，本质上调用的就是 `updateReducer`，如上会把待更新的队列 `pendingQueue` 拿出来，合并到 `baseQueue`，循环进行更新。

循环更新的流程，说白了就是执行每一个 `num => num + 1` ，得到最新的 `state` 。接下来就可以从 `useState` 中得到最新的值。

![state更新](https://p.ipic.vip/ogjs7r.jpg)

??



## 组件的比较

### 类组件中的 `setState` 和函数组件中的 `useState` 有什么异同？ 

**相同点：**

- 从原理角度出发，`setState` 和 `useState` 更新视图，底层都调用了 `scheduleUpdateOnFiber` 方法，而且事件驱动情况下都有批量更新规则。

**不同点：**

- 浅比较更新：在不是 `pureComponent` 类组件模式下， `setState` 不会浅比较两次 `state` 的值，只要调用 `setState`，在没有其他优化手段的前提下，就会执行更新。但是 `useState` 中的 `dispatchAction` 会默认比较两次 `state` 是否相同，然后决定是否更新组件。

- `setState` 有专门监听 `state` 变化的回调函数 `callback`，可以获取最新 `state`；但是在函数组件中，只能通过 `useEffect` 来执行 `state` 变化引起的副作用。

- `setState` 在底层处理逻辑上主要是和老 `state` 进行合并处理，而 `useState` 更倾向于重新赋值。



