## Vue.js内部运行机制

> 1、初始化及挂载：首先在new Vue之后，会调用_init函数进行生命周期、事件、props、methods、data、computed、watch等的初始化。其中最重要的是通过Object.defineProperty设置getter和setter函数，实现响应式和依赖收集。然后调用$mount挂载组件。
>
> 2、编译：分为parse、optimize、generate三个阶段，parse是通过正则等方式解析template模板中的指令，将HTML字符串转化为AST；optimize主要作用是标记static静态节点，是Vue在编译过程中的一处优化，当更新界面时，会有一个patch过程，diff算法会直接跳过静态节点，减少了比较过程，优化了patch性能；generate是将AST转化为render function的过程。
>
> 3、响应式：使用Object.defineProperty设置getter和setter函数，当 render function 被渲染的时候，当读取所需对象的值的时候会执行getter函数进行依赖收集，目的是将观察者 watcher 对象存放到当前闭包中的订阅者Dep的subs中。在修改值的时候会执行setter函数，通知之前依赖收集得到的Dep中的每一个watcher，告诉它们自己的值变了，需要重新更新渲染视图，这时候watcher就会调用uodate来更新视图。
>
> 4、Virtual DOM：当render function后会被转化为 VNode 节点，Virtual DOM其实就是一颗以JavaScript对象作为基础的树，用对象属性来描述节点，实际上它只是一层对真实DOM的抽象，最终可以通过一系列操作使这棵树映射到真实环境上。
>
> 5、更新视图：当数据变化后，执行 render function 就可以得到一个新的 VNode 节点，我们如果想要得到新的视图，最直接的方法就是可以通过解析这个新的 VNode 节点，然后用innerHTML 直接全部渲染到真实的DOM中，但是其实我们只对其中的一小块内容做修改，这么做就有点浪费，那我们就可以只修改那些改变的地方，这时就可以用到patch，可以将新的VNode和旧的VNode一起传入patch进行比较，经过diff算法得出他们之间的差异，然后值修改这些差异对应的DOM即可。

![img](https://user-gold-cdn.xitu.io/2017/12/19/1606e7eaa2a664e8?imageslim)

### 初始化及挂载

在`new Vue()`之后，`Vue`会调用`__init`函数进行初始化，会初始化生命周期、事件、props、methods、data、computed与watch等。

```js
// vue-src/core/instance/index.js
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  /*初始化*/
  this._init(options)
}
initMixin(Vue) // 在Vue的原型上增加_init方法，构造Vue实例的时候会调用这个_init方法来初始化Vue实例
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)
```

> 其中最重要的是通过`Object.defineProperty`设置`getter`和`setter`函数，用来实现**响应式**和**依赖收集**。

初始化之后调用`$mount`会挂载组件，如果是运行时编译，即不存在`render function`但是存在`template`的情况，需要进行编译步骤。

```js
// vue-src/core/instance/init.js
if (vm.$options.el) {
      /*挂载组件*/
      vm.$mount(vm.$options.el)
    }
```

### 编译

`complile`编译可以分成`parse`、`optimize`、`generate`三个阶段，最终需要得到`render function`。

#### parse

`parse` 会用**正则等方式**解析 template 模板中的指令、class、style等数据，**将HTML字符串转换成AST。**

```js
// vue-src/compiler/parser/index.js
```

#### optimize

**`optimize` 的主要作用是标记 static 静态节点**，这是 Vue 在编译过程中的一处优化，后面当 `update` 更新界面时，会有一个 `patch` 的过程， diff 算法会直接跳过静态节点，从而**减少了比较的过程**，**优化了 `patch` 的性能**。

```js
// vue-src/compiler/optimizer.js
/*
  将AST树进行优化
  优化的目标：生成模板AST树，检测不需要进行DOM改变的静态子树。
  一旦检测到这些静态树，我们就能做以下这些事情：
  1.把它们变成常数，这样我们就再也不需要每次重新渲染时创建新的节点了。
  2.在patch的过程中直接跳过。
 */
export function optimize (root: ?ASTElement, options: CompilerOptions) {
  if (!root) return
  /*标记是否为静态属性*/
  isStaticKey = genStaticKeysCached(options.staticKeys || '')
  /*标记是否是平台保留的标签*/
  isPlatformReservedTag = options.isReservedTag || no
  // first pass: mark all non-static nodes.
  /*处理所有非静态节点*/
  markStatic(root)
  // second pass: mark static roots.
  /*处理static root*/
  markStaticRoots(root, false)
}
```

#### generate

**`generate` 是将 AST 转化成 `render function` 字符串的过程**，得到结果是 `render `的字符串以及 `staticRenderFns` 字符串。

```js
// vue-src/compiler/codegen/index.js
/*将AST语法树转化成render以及staticRenderFns的字符串*/
export function generate (
  ast: ASTElement | void,
  options: CompilerOptions
): {
  render: string,
  staticRenderFns: Array<string>
} {
  // save previous staticRenderFns so generate calls can be nested
  const prevStaticRenderFns: Array<string> = staticRenderFns
  const currentStaticRenderFns: Array<string> = staticRenderFns = []
  const prevOnceCount = onceCount
  onceCount = 0
  currentOptions = options
  warn = options.warn || baseWarn
  transforms = pluckModuleFunction(options.modules, 'transformCode')
  dataGenFns = pluckModuleFunction(options.modules, 'genData')
  platformDirectives = options.directives || {}
  isPlatformReservedTag = options.isReservedTag || no
  const code = ast ? genElement(ast) : '_c("div")'
  staticRenderFns = prevStaticRenderFns
  onceCount = prevOnceCount
  return {
    render: `with(this){return ${code}}`,
    staticRenderFns: currentStaticRenderFns
  }
}
```

### 响应式

在`Vue`初始化的时候，会通过`Object.defineProperty`设置`setter`和`getter`函数，它使得当被设置的对象被读取的时候会执行 `getter` 函数，而在当被赋值的时候会执行 `setter` 函数。

当 render function 被渲染的时候，因为会读取所需对象的值，所以会触发 `getter` 函数进行依赖收集。

> 依赖收集的目的是将观察者 Watcher 对象存放到当前闭包中的订阅者 Dep 的 subs 中。形成如下所示的这样一个关系。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gj4ene1jg1j30eg06tjrb.jpg)

在修改对象的值的时候，会触发对应的 `setter`， `setter` 通知之前「**依赖收集**」得到的 Dep 中的每一个 Watcher，告诉它们自己的值改变了，需要重新渲染视图。这时候这些 Watcher 就会开始调用 `update` 来更新视图，当然这中间还有一个 `patch` 的过程以及使用队列来异步更新的策略。

```js
// vue-src/core/instance/state.js
/*通过proxy函数将_data（或者_props等）上面的数据代理到vm上，这样就可以用app.text代替app._data.text了。*/
export function proxy (target: Object, sourceKey: string, key: string) {
  sharedPropertyDefinition.get = function proxyGetter () {
    return this[sourceKey][key]
  }
  sharedPropertyDefinition.set = function proxySetter (val) {
    this[sourceKey][key] = val
  }
  Object.defineProperty(target, key, sharedPropertyDefinition)
}

/*初始化props、methods、data、computed与watch*/
export function initState (vm: Component) {
  vm._watchers = []
  const opts = vm.$options
  /*初始化props*/
  if (opts.props) initProps(vm, opts.props)
  /*初始化方法*/
  if (opts.methods) initMethods(vm, opts.methods)
  /*初始化data*/
  if (opts.data) {
    initData(vm)
  } else {
    /*该组件没有data的时候绑定一个空对象*/
    observe(vm._data = {}, true /* asRootData */)
  }
  /*初始化computed*/
  if (opts.computed) initComputed(vm, opts.computed)
  /*初始化watchers*/
  if (opts.watch) initWatch(vm, opts.watch)
}

```

### Virtual DOM

我们知道，render function 会被转化成 VNode 节点。Virtual DOM 其实就是一棵以 JavaScript 对象（ VNode 节点）作为基础的树，用对象属性来描述节点，实际上它只是一层对真实 DOM 的抽象。最终可以通过一系列操作使这棵树映射到真实环境上。由于 Virtual DOM 是以 JavaScript 对象为基础而不依赖真实平台环境，所以使它具有了跨平台的能力，比如说浏览器平台、Weex、Node 等。

比如说下面这样一个例子：

```js
{
    tag: 'div',                 /*说明这是一个div标签*/
    children: [                 /*存放该标签的子节点*/
        {
            tag: 'a',           /*说明这是一个a标签*/
            text: 'click me'    /*标签的内容*/
        }
    ]
}
```

渲染后可以得到

```js
<div>
    <a>click me</a>
</div>
```

这只是一个简单的例子，实际上的节点有更多的属性来标志节点，比如 isStatic （代表是否为静态节点）、 isComment （代表是否为注释节点）等。

### 更新视图

前面我们说到，在修改一个对象值的时候，会通过 `setter -> Watcher -> update` 的流程来修改对应的视图，那么最终是如何更新视图的呢？

当数据变化后，执行 render function 就可以得到一个新的 VNode 节点，我们如果想要得到新的视图，最简单粗暴的方法就是直接解析这个新的 VNode 节点，然后用 `innerHTML` 直接全部渲染到真实 DOM 中。但是其实我们只对其中的一小块内容进行了修改，这样做似乎有些「**浪费**」。

那么我们为什么不能只修改那些「改变了的地方」呢？这个时候就要介绍我们的「**`patch`**」了。我们会将新的 VNode 与旧的 VNode 一起传入 `patch` 进行比较，经过 diff 算法得出它们的「**差异**」。最后我们只需要将这些「**差异**」的对应 DOM 进行修改即可。

## 响应式系统的基本原理

核心主要是通过`Object.defineProperty`实现响应式系统。

首先定义一个cb函数用来模拟视图更新，然后定义一个defineReactive，这个方法通过Object.defineProperty来实现对对线的响应式化，入参是一个 obj（需要绑定的对象）、key（obj的某一个属性），val（具体的值）。经过 `defineReactive` 处理以后，我们的 obj 的 key 属性在「读」的时候会触发 `reactiveGetter` 方法，而在该属性被「写」的时候则会触发 `reactiveSetter` 方法。接下来，我们需要在上面再封装一层 `observer` 。这个函数传入一个 value（需要「**响应式**」化的对象），通过遍历所有属性的方式对该对象的每一个属性都通过 `defineReactive` 处理。

```js
let vue = new Vue({
  data: {
    test: "I am test."
  }
});
vue._data.test = "hello world.";

class Vue {
  // Vue构造类
  constructor(options) {
    this._data = options.data;
    observer(this._data);
  }
}

function observer(value) {
  if (!value || (typeof value !== 'object')) {
    return;
  }
  
  Object.keys(value).forEach((key) => {
    defineReactive(value, key, value[key]);
  })
}

// 模拟视图更新函数
function cb(val) {
  // 渲染视图
  console.log("视图更新啦~");
}

// 
function defineReactive(obj, key, val) {
  Object.defineProperty(obj, key, {
    // 属性可枚举
    enumerable: true,
    // 属性可被修改或删除
    configurable: true,
    get: function rectiveGetter() {
      // 会依赖收集
      return val;
    },
    set: fucntion rectiveSetter(newVal) {
    	if (newVal === val) return;
    	cb(newVal);
  }
  });
}
```

## Vue2.0和Vue3.0的区别

**1.项目目录结构**
vue-cli2.0与3.0在目录结构方面，有明显的不同

vue-cli3.0移除了配置文件目录，`config` 和 `build` 文件夹

同时移除了 `static` 静态文件夹，新增了 `public` 文件夹，打开层级目录还会发现， `index.html` 移动到 `public` 中

**2.配置项**
3.0 config文件已经被移除，但是多了.env.production和env.development文件，除了文件位置，实际配置起来和2.0没什么不同

没了config文件，跨域需要配置域名时，从config/index.js 挪到了vue.config.js中，配置方法不变

**3.渲染**
Vue2.x使用的Virtual Dom实现的渲染

Vue3.0不论是原生的html标签还是vue组件，他们都会通过h函数来判断，如果是原生html标签，在运行时直接通过Virtual Dom来直接渲染，同样如果是组件会直接生成组件代码
**4.数据监听**
Vue2.x大家都知道使用的是es5的object.defineproperties中getter和setter实现的，而vue3.0的版本，是基于Proxy进行监听的，其实基于proxy监听就是所谓的lazy by default，什么意思呢，就是只要你用到了才会监听，可以理解为‘按需监听’，官方给出的诠释是：速度加倍，同时内存占用还减半。

**4.按需引入**
Vue2.x中new出的实例对象，所有的东西都在这个vue对象上，这样其实无论你用到还是没用到，都会跑一变。而vue3.0中可以用ES module imports按需引入，如：keep-alive内置组件、v-model指令，等等。

## vue、react、angular区别和联系

##### 联系：

1、都是提倡组件化开发的框架。

2、都是数据驱动视图。属于MVVM框架，在开发中，我们只需要关注数据变化即可(使用方式不尽相同，react 属于函数式，angular 和vue 属于声明式编程)。

3、共同的开发套路。比如，都有父子组件传递，都有数据管理框架，都有前端路由，都有插槽等。

##### 区别：

| 区别                   | Vue                                                          | React                                                        | Angular         |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------- |
| 模板渲染               | HTML模板编写                                                 | JSX（JavaScript XML）渲染模板                                | 模板编写        |
| 底层渲染               | 虚拟DOM                                                      | 虚拟DOM                                                      | Incremental DOM |
| 编程思想               | 声明式编程                                                   | 函数式编程                                                   | 声明式编程      |
| 社区复杂度             | 社区活跃，较简单                                             | 不如Vue活跃，复杂                                            | 社区活跃        |
| 适合开发               | 主要关注Web开发，但是为了支持其他平台向前发展而编写          | Web和Native                                                  |                 |
| 功能                   | Web应用程序框架，能够为高级单页面应用提供支持                | 可用作开发单页面或移动应用程序的基础                         |                 |
| 显著特点               | 基于HTML的模板；反应；组件；路由；集成                       | 单向数据绑定；有状态的组件；虚拟DOM；JSX                     |                 |
| 监听数据变化的实现原理 | Vue 通过 getter/setter 以及一些函数的劫持，能精确知道数据变化，不需要特别的优化就能达到很好的性能 | React 默认是通过比较引用的方式进行的，如果不优化可能导致大量不必要的VDOM的重新渲染 |                 |
| 组件通信               | 更倾向于使用事件                                             | 使用回调函数                                                 |                 |
| 适用场景               | 更偏向于简单迅速的解决问题，小型项目                         | 更偏向于构建稳定大型的应用                                   |                 |

?> 掘金：https://juejin.im/post/6844903668446134286

## MVVM模式

MVVM 是 Model-View-ViewModel 的缩写。

![img](https://tva1.sinaimg.cn/large/007S8ZIlly1gied878bssj30o50c2wgb.jpg)

- Model：负责数据存储
- View：负责页面展示
- View Model：负责业务逻辑处理（比如Ajax请求等），对数据进行加工后交给视图展示

在 MVVM 架构下，View 和 Model 之间并没有直接的联系，而是通过 ViewModel 进行交互，Model 和 ViewModel 之间的交互是双向的， 因此 View 数据的变化会同步到 Model 中，而 Model 数据的变化也会立即反应到 View 上。

**ViewModel 通过双向数据绑定把 View 层和 Model 层连接了起来**，而 View 和 Model 之间的同步工作完全是自动的，无需人为干涉，因此**开发者只需关注业务逻辑，不需要手动操作 DOM, 不需要关注数据状态的同步问题**，复杂的数据状态维护完全由 MVVM 来统一管理。

> Vue核心：数据驱动，避免手动操作DOM元素，这样可以让前端程序员主要关注在数据的业务逻辑实现上，而不是关系DOM如何渲染。

## vuex 相关

### vuex 有哪几种属性

有 5 种，分别是 state、getter、mutation、action、module

### vuex 的 store 特性是什么

- vuex 就是一个仓库，仓库里放了很多对象。其中 state 就是数据源存放地，对应于一般 vue 对象里面的 data
- state 里面存放的数据是响应式的，vue 组件从 store 读取数据，若是 store 中的数据发生改变，依赖这相数据的组件也会发生更新
- 它通过 mapState 把全局的 state 和 getters 映射到当前组件的 computed 计算属性

### vuex 的 getter 特性是什么

- getter 可以对 state 进行计算操作，它就是 store 的计算属性
- 虽然在组件内也可以做计算属性，但是 getters 可以在多给件之间复用
- 如果一个状态只在一个组件内使用，是可以不用 getters

### vuex 的 mutation 特性是什么

- action 类似于 muation, 不同在于：action 提交的是 mutation,而不是直接变更状态
- action 可以包含任意异步操作

### vue 中 ajax 请求代码应该写在组件的 methods 中还是 vuex 的 action 中

如果请求来的数据不是要被其他组件公用，仅仅在请求的组件内使用，就不需要放入 vuex 的 state 里

如果被其他地方复用，请将请求放入 action 里，方便复用，并包装成 promise 返回

### 不用 vuex 会带来什么问题

- 可维护性会下降，你要修改数据，你得维护 3 个地方
- 可读性下降，因为一个组件里的数据，你根本就看不出来是从哪里来的
- 增加耦合，大量的上传派发，会让耦合性大大的增加，本来 Vue 用 Component 就是为了减少耦合，现在这么用，和组件化的初衷相背

### vuex 原理

vuex 仅仅是作为 vue 的一个插件而存在，不像 Redux,MobX 等库可以应用于所有框架，vuex 只能使用在 vue 上，很大的程度是因为其高度依赖于 vue 的 computed 依赖检测系统以及其插件系统，

vuex 整体思想诞生于 flux,可其的实现方式完完全全的使用了 vue 自身的响应式设计，依赖监听、依赖收集都属于 vue 对对象 Property set get 方法的代理劫持。最后一句话结束 vuex 工作原理，vuex 中的 store 本质就是没有 template 的隐藏着的 vue 组件；

## 虚拟 DOM

传统的web开发，是利用 jQuery操作DOM，这是非常耗资源的。

我们可以在 JS 的内存里构建类似于DOM的对象，去拼装数据，拼装完整后，把数据整体解析，一次性插入到html里去。这就形成了虚拟 DOM。

Vue1.0没有虚拟DOM，Vue2.0改成了基于虚拟DOM。

## Vue框架的特点

- 模板渲染：基于 html 的模板语法，学习成本低。
- 响应式的更新机制：数据改变之后，视图会自动刷新。【重要】
- 渐进式框架
- 组件化/模块化
  - 模块化：是从代码逻辑的角度进行划分的；方便代码分层开发，保证每个功能模块的职能单一
  - 组件化：是从UI界面的角度进行划分的；前端的组件化，方便UI组件的重用
- 轻量：开启 gzip压缩后，可以达到 20kb 大小。（React 达到 35kb，AngularJS 达到60kb）。

## vue 的优点

- 低耦合。视图（View）可以独立于 Model 变化和修改，一个 ViewModel 可以绑定到不同的"View"上，当 View 变化的时候 Model 可以不变，当 Model 变化的时候 View 也可以不变。
- 可重用性。你可以把一些视图逻辑放在一个 ViewModel 里面，让很多 view 重用这段视图逻辑。
- 独立开发。开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计，使用 Expression Blend 可以很容易设计界面并生成 xml 代码。
- 可测试。界面素来是比较难于测试的，而现在测试可以针对 ViewModel 来写。

## Vue生命周期

定义：从Vue实例创建、运行、到销毁期间，总是伴随着各种各样的事件，这些事件，统称为[vue实例的生命周期](https://cn.vuejs.org/v2/guide/instance.html#实例生命周期)。

- [生命周期钩子](https://cn.vuejs.org/v2/api/#选项-生命周期钩子)：就是生命周期事件的别名而已。

生命周期钩子 = 生命周期函数 = 生命周期事件。

![截屏2020-09-0420.14.57](https://tva1.sinaimg.cn/large/007S8ZIlly1gievrn5ps8j30si0hc406.jpg)

#### 1、创建期间的生命周期函数

- beforeCreate：实例刚在内存中被创建出来，此时，还没有初始化好 data 和 methods 属性
- created：实例已经在内存中创建OK，此时 data 和 methods 已经创建OK，此时还没有开始 编译模板。我们可以在这里进行Ajax请求。
- beforeMount：此时已经完成了模板的编译，但是还没有挂载到页面中
- mounted：此时，已经将编译好的模板，挂载到了页面指定的容器中显示。（mounted之后，表示**真实DOM渲染完了，可以操作DOM了**）

![截屏2020-09-0415.18.26](https://tva1.sinaimg.cn/large/007S8ZIlly1gien6iodd5j30w004k0t5.jpg)

#### 2、运行期间的生命周期函数

- beforeUpdate：状态更新之前执行此函数， 此时 data 中的状态值是最新的，但是界面上显示的 数据还是旧的，因为此时还没有开始重新渲染DOM节点
- updated：实例更新完毕之后调用此函数，此时 data 中的状态值 和 界面上显示的数据，都已经完成了更新，界面已经被重新渲染好了。

PS：数据发生变化时，会触发这两个方法。不过，我们一般用watch来做。

![截屏2020-09-0416.53.13](https://tva1.sinaimg.cn/large/007S8ZIlly1giepx2sjw8j30u806sq40.jpg)

#### 3、销毁期间的生命周期函数

- beforeDestroy：实例销毁之前调用。在这一步，实例仍然完全可用。
- destroyed：Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。

PS：可以在beforeDestroy里**清除定时器、或清除事件绑定**。

## v-bind和v-model的区别

* v-bind用来绑定数据和属性以及表达式，缩写`:`

* v-model使用在表单中，实现双向数据绑定的，在表单元素外使用不起作用

## 组件之间的传值

* 父组件与子组件传值：父组件通过标签上面定义传值；子组件通过props方法接受数据
* 子组件向父组件传递数据：子组件通过$$

## vue 的双向绑定的原理

vue.js 是采用数据劫持结合发布者-订阅者模式的方式，通过 Object.defineProperty()来劫持各个属性的 setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

具体步骤： 第一步：需要 observe 的数据对象进行递归遍历，包括子属性对象的属性，都加上 setter 和 getter 这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到了数据变化

第二步：compile 解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图

第三步：Watcher 订阅者是 Observer 和 Compile 之间通信的桥梁，主要做的事情是:

- 在自身实例化时往属性订阅器(dep)里面添加自己
- 自身必须有一个 update()方法
- 待属性变动 dep.notice()通知时，能调用自身的 update() 方法，并触发 Compile 中绑定的回调，则功成身退。

第四步：MVVM 作为数据绑定的入口，整合 Observer、Compile 和 Watcher 三者，通过 Observer 来监听自己的 model 数据变化，通过 Compile 来解析编译模板指令，最终利用 Watcher 搭起 Observer 和 Compile 之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据 model 变更的双向绑定效果。

## v-show 与 v-if 区别

区别：

**v-if** 是**真正**的条件渲染，如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

**v-show** 不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 的 “display” 属性进行切换。

适用场景：

v-if 适用于在运行时很少改变条件，不需要频繁切换条件的场景；

v-show 则适用于需要非常频繁切换条件的场景。

## Class 与 Style 如何动态绑定

都可以通过**对象语法和数组语法**进行动态绑定

#### Class

```js
// 对象语法
<div v-bind:class="{active: isActive, 'text-dangeer': hasError}"></div>
data: {
  isActive: true,
  hasError: false
}
// 数组语法
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

#### Style

```js
// 对象语法
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
data: {
  activeColor: 'red',
  fontSize: 30
}
// 数组语法
<div v-bind:style="[styleColor, styleSize]"></div>
data: {
  styleColor: {
     color: 'red'
   },
  styleSize:{
     fontSize:'23px'
  }
}
```

## vue双向数据绑定的实现

vue内部利用Object.defineProperty监听数据变化，使数据具有可观测性，结合发布订阅模式，在数据发生变化时更新视图

- 利用Proxy或Object.defineProperty生成的Observer针对对象/对象的属性进行"劫持",在属性发生变化后通知订阅者
- 解析器Compile解析模板中的Directive(指令)，收集指令所依赖的方法和数据,等待数据变化然后进行渲染
- Watcher属于Observer和Compile桥梁,它将接收到的Observer产生的数据变化,并根据Compile提供的指令进行视图渲染,使得数据变化促使视图变化



```
// 简单的双向数据绑定
const data = {};
Object.keys(data).forEach(function(key) {
    Object.defineProperty(data, key, {
        enumerable: true,
        configurable: true,
        get: function() {
            console.log('get');
        },
        set: function(newVal) {
            // 当属性值发⽣变化时我们可以进⾏额外操作
        },
    });
});
```

## Object.defineProperty和proxy的优劣区别

Object.defineProperty兼容性较好，但不能直接监听数组的变化，只能监听对象的属性(有时需要深层遍历)

与之相比proxy的优点：

- 可以直接监听数组的变化
- 可以直接监听对象而非属性
- proxy有多达13种的拦截方法，不限于apply、ownKeys、deleteProperty、has等等
- proxy受到各大浏览器厂商的重视

## 除了数据劫持，vue为什么还需要虚拟DOM进行diff检测差异

现代前端框架主要有两种监听数据的方式：一种是pull的方式，一种是push的方式

pull，其代表为react，react和vue基于双向数据绑定的依赖收集的订阅式机制不同，react是通过显式的触发函数调用来更新视图，比如setState，然后React会一层层的进行Virtual Dom Diff操作找出差异，通过较暴力diff的方式查找哪里发生变化。另一个代表是angular的脏值检测

push，其代表为vue，当Vue程序初始化的时候就会对数据data进行依赖的收集，一但数据发生变化，响应式系统就会立刻得知；我们知道绑定一个数据通常就需要一个watcher，那么一旦细粒度过高会产生大量的watcher，会给增加内存以及依赖追踪的开销，而细粒度过低会无法精准检测变化，因此vue选择中细粒度方案，在组件级进行push检测的方式(即依赖响应式系统)，在组件内部进行Virtual Dom Diff获取更加具体的差异，所以vue采用了push+pull结合的方式

## 对vue响应式系统的理解

![img](https://cdn.jsdelivr.net/gh/okaychen/CDN@2.2/brochure/image/data.png)

vue响应系统

响应式系统简述：

- 任何⼀个 Vue Component 都有⼀个与之对应的 Watcher 实例
- Vue 的 data 上的属性会被添加 getter 和 setter 属性
- 当Vue Component render函数被执⾏的时候，data上会被触碰(touch)， 即被读，getter ⽅法会被调⽤， 此时 Vue 会去记录此 Vue component所依赖的所有data(这⼀过程被称为依赖收集)
- data 被改动时(主要是⽤户操作)， setter ⽅法会被调⽤， 此时 Vue 会去通知所有依赖于此 data 的组件去调⽤他们的 render 函数进⾏更新

## vue的生命周期

vue 实例有⼀个完整的⽣命周期，也就是从开始创建、初始化数据、编译模版、挂载Dom -> 渲染、更新 -> 渲染、卸载等⼀系列过程，即vue实例从创建到销毁的过程，我们称这是vue的⽣命周期

![img](https://cdn.jsdelivr.net/gh/okaychen/CDN@2.2/brochure/image/lifecycle.png)

img

1.beforeCreate：完成实例初始化，初始化非响应式变量

2.created：实例初始化完成(未挂载DOM)

3.berofeMount：找到对应的template，并编译成render函数

4.mounted：完成创建vm.$el和双向绑定，完成DOM挂载

5.beforeUpdate：数据更新之前(可在更新前访问现有的DOM)

6.updated：完成虚拟DOM的重新渲染和打补丁

7.activated：子组件需要在每次加载时候进行某些操作，可以使用activated钩子触发

8.deactivated：keep-alive 组件被移除时使用

9.beforeDestroy：可做一些删除提示，销毁定时器，解绑全局时间 销毁插件对象

10.destroyed：当前组件已被销毁

## computed和watch区别

当我们要进⾏数值计算,⽽且依赖于其他数据，我们需要使用computed

如果你需要在某个数据变化时做⼀些事情，使⽤watch来观察这个数据

computed：

- 1.是计算值，
- 2.应用：就是简化tempalte里面计算和处理props或$emit的传值
- 3.具有缓存性，页面重新渲染值不变化,计算属性会立即返回之前的计算结果，而不必再次执行函数

watch：

- 1.是观察的动作，
- 2.应用：监听props，$emit或本组件的值执行异步操作
- 3.无缓存性，页面重新渲染时值不变化也会执行

## vue指令，v-if和v-for能不能一起使用

v-html，v-text，v-show，v-for，v-if v-else-if v-else，

v-bind（用来动态的绑定一个或者多个特性）

v-model（创建双向数据绑定）

v-cloak（保持在元素上直到关联实例结束时进行编译）

v-pre（用来跳过这个元素和它的子元素编译过程）

> v-if和v-for能不能一起使用(或者问v-for和v-if谁的优先级更高)：
>
> v-for指令的优先级要高于v-if，当处于同一节点时候，意味着v-if将分别重复运行于每个 v-for 循环中，所以应该尽量避免v-for和v-if在同一结点

## vue中的key值有什么用(diff算法的过程)

vue采用“就地复用”策略，如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素，key 的作用主要是为了高效的更新虚拟DOM。

> 虚拟dom的diff算法的过程中，先会进⾏新旧节点的⾸尾交叉对⽐，当⽆法匹配的时候会⽤新节点的 key 与旧节点进⾏⽐对，然后超出差异

![img](https://cdn.jsdelivr.net/gh/okaychen/CDN@2.2/brochure/image/webp)

img

vue的diff位于patch.js中，这里简单总结一下patchVnode比较的的过程，首先要判断vnode和oldVnode是否都存在，都存在并且vnode和oldVnode是同一节点时，才会进入patchVnode进行比较，结点比较五种情况：

① 引用一致，可以认为没有变化

② 文本节点的比较，如果需要修改：则会调用Node.textContent = vnode.text

③ 两个节点都有子节点，而且它们不一样：则调用updateChildren函数比较子节点

④ 只有新的节点有子节点：则调用addVnodes创建子节点

⑤ 只有老节点有子节点，则调用removeVnodes把这些子节点都删除

updateChildren的过程：updateChildren用指针的方式把新旧节点的子节点的首尾节点标记，即oldStartIndex(1)，oldEndIndex(2)，newStartIndex(3), oldEndIndex(4)（这里简单用1 2 3 4顺序标记）即依次比较13，14，23，24，有10种左右情况分别做出对应的处理

## vue的路由实现

更新视图但不重新请求页面，是前端路由原理的核心，目前在浏览器环境主要有两种方式：

- Hash 模式

hash("#")符号的本来作用是加在URL指示网页中的位置：



```
http://www.example.com/index.html#print
```

`#`本身以及它后面的字符称之为hash，可通过window.location.hash属性读取，hash虽然在url中，但是却不会被包含在http请求中，也不会重新加载页面，它用来指导浏览器动作

- History 模式

History interface是浏览器历史记录栈提供的接口，从HTML5开始，History interface提供了2个新的方法：`pushState()`，`replaceState()`使得我们可以对浏览器历史记录栈进行修改；这两个方法有有一个特点，当调用他们修改浏览器历史栈后，虽然当前url改变了，但浏览器不会立即发送请求该url，这就为单页应用前端路由，更新视图但不重新请求页面提供了基础

## vue组件间通信的方式

- props/$emit+v-on

  父组件通过props的方式向子组件传递数据，而通过$emit 子组件可以向父组件通信

- eventBus

  通过eventBus向中心事件发送或者接收事件，所有事件都可以共用事件中心

- vuex

  状态管理模式，采用集中式存储管理应用的所有组件的状态，可以通过vuex管理全局的数据

> 参考：[vue中8种组件通信方式](https://juejin.im/post/5d267dcdf265da1b957081a3) 只需要熟悉上面三种即可，其他基本不会用到，可以做为了解

## vue路由懒加载的方式

懒加载简单来说就是延迟加载或按需加载，即在需要的时候的时候进行加载，常用的懒加载方式有三种：即使用vue异步组件 和 ES6中的import，以及webpack的require.ensure()

- vue异步组件

  

  ```
  // 路由配置，使用vue异步组件
  {
      path: '/home',
      name: 'home',
      component: resolve => require(['@/components/home'],resolve)
  }
  ```

- ES6中的import

  

  ```
  // 指定了相同的webpackChunkName，合并打包成一个js文件
  // 如果不指定，则分开打包
  const Home = () => import(/*webpackChunkName:'ImportFuncDemo'*/ '@/components/home')
  const Index = () => import(/*webpackChunkName:'ImportFuncDemo'*/ '@/components/index')
  ```

- webpack推出的require.ensure()

  

  ```
  {
      path: '/home',
      name: 'home',
      component: r => require.ensure([], () => r(require('@/components/home')), 'demo')
  }
  ```

## 前端性能优化的方案

前端性能优化七大常用的优化手段：减少请求数量，减小资源大小，优化网络连接，优化资源加载，减少回流重绘，使用更好性能的API和构建优化

- ##### 减少请求数量

  - 文件合并，并按需分配（公共库合并，其他页面组件按需分配）
  - 图片处理：使用雪碧图，将图片转码base64内嵌到HTML中，使用字体图片代替图片
  - 减少重定向：尽量避免使用重定向，当页面发生了重定向，就会延迟整个HTML文档的传输
  - 使用缓存：即利用浏览器的强缓存和协商缓存
  - 不使用CSS @import：CSS的@import会造成额外的请求
  - 避免使用空的src和href：a标签设置空的href，会重定向到当前的页面地址，form设置空的method，会提交表单到当前的页面地址

- ##### 减小资源大小

  - 压缩：静态资源删除无效冗余代码并压缩
  - webp：更小体积
  - 开启gzip：HTTP协议上的GZIP编码是一种用来改进WEB应用程序性能的技术

- ##### 优化网络连接

  - 使用CDN
  - 使用DNS预解析：DNS Prefetch，即DNS预解析就是根据浏览器定义的规则，提前解析之后可能会用到的域名，使解析结果缓存到`系统缓存`中，缩短DNS解析时间，来提高网站的访问速度
  - 并行连接：由于在HTTP1.1协议下，chrome每个域名最大并发数是6个。使用多个域名，可以增加并发数
  - 管道化连接：在HTTP2协议中，可以开启管道化连接，即单条连接的多路复用

  

  优化资源加载

  减少重绘回流

  使用性能更好的API

  构建优化：如webpack优化










