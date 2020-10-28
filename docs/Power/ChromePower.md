## 允许重复声明 let 和 class

chrome最新版本，在控制台使用let或class对变量进行二次声明，不会报错。更新版本之前，不能进行重复声明。

![image-20201028161742089](https://i.loli.net/2020/10/28/Jcu6ZCIQhbeNXTS.png)

!> 最新版本：版本 86.0.4240.111（正式版本） （64 位）

## 过滤请求

网页请求服务器，有时候发起的请求太多，我们想知道哪些请求返回阻塞了，可以对请求的网络进行过滤，来定位问题。

!> 控制面板 => Network => filter图标 => is:running => 刷新监控的页面

![image-20201028162337963](https://i.loli.net/2020/10/28/7TPOgB1GZLznjs4.png)

## 展开DOM所有的子节点

在进行`DOM`节点元素调试的时候，我们需要对每个节点进行展开查看，如果只是逐个点击目标元素下面的子元素展开，耗费时间。可以尝试下面的快捷操作。

!>mac: 控制面板 => Elements => 按option + 点击要展开的元素图标

window：控制面板 => Elements => 按Alt+ 点击要展开的元素图标

## 预设设备

在进行调试的时候，特别是开发移动端，在没有充足调试机的情况下，我们就靠调试工具进行模拟。那么，除了谷歌浏览器默认设备的几个值，比如`iPhone X, iPad`。我们还可以自定义自己需要的设备。

> 控制面板 => setting图标 => Devices => Add custom device...

![image-20201028164939888](https://i.loli.net/2020/10/28/oGXFI6KWmYTLbBc.png)



## 预设网络状况

我们不能把控用户使用我们产品的网络下载速度，所以我们得模拟不同网速下面的产品表现情况，以检查我们对产品的优化是否符合预期效果。同理，我们也可以自定义网络的状况，一般情况下默认是`online`。

!> 控制面板 => setting图标 => Throttling => Add custom profile...

![image-20201028201134829](https://i.loli.net/2020/10/28/R4XrnDdCQiBpTlS.png)

### 7. 捕获快照

#### 7.1 捕获全屏快照

!> 控制面板 => command + shift + p => capture full size screenshot

#### 7.2 捕获局部快照

!> 控制面板 => 审查元素 => command + shift + p => capture node screenshot

## 快速清空站点缓存

有时候开发调试，我们需要清空缓存信息。与其手动一个个信息清空，还不如一步到位，直接清空这个站点的信息 

> 控制面板 => command + shift + p => clear site data

## 更改调试面板主题

在开发调试中，默认主题难免让眼睛审美疲劳。而且，作为一个开发者，要高冷，高冷，高冷...暗黑色调妥妥的。通过下面的操作，你可以选择适合自己的风格。

> 控制面板 => setting设置图标 => Preferences => Appearance => Theme