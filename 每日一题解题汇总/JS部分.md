# 1.commonjs的模块机制和es6模块机制的区别?

1. CommonJS 是运行时加载，ES6 是编译时加载。原因：CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。
2. 语法不同。commonjs使用`require、module.exports`关键字导入导出， es模块使用`import、export`关键字导入导出。
3. CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。注意：CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块。
4. CommonJS 模块的require()是同步加载模块，ES6 模块的import命令是异步加载，有一个独立的模块依赖的解析阶段。

**参考文章**

- [commonJS和ES6模块化的区别](https://blog.csdn.net/xieanna123/article/details/110952381)



# 2.Object.defineProperty和Proxy的区别，Vue3 为什么用Proxy替代Object.defineProperty？

## 2.1 区别

### Object.defineProperty ：

- 必须遍历对象的每个属性，不能监听对象本身；
- 无法监听到对象新增的属性；
- 无法监听数组变化（数组的这些方法不能触发set:push、pop、shift、unshift、splice、sort、reverse）。Vue对其变异方法进行重写；
- Vue2使用Object.defineProperty对所有对象的属性进行遍历，性能较差；

### Proxy：

- 监听对象本身；
- 可以监听到对象新增的属性；
- 可以监听到数组变化；
- 性能较好；
- 监听的事件类型更多；

## 2.2 Vue3使用Proxy的原因：

- 可以直接监听对象本身，不需要遍历循环对象的每个属性，减少代码体积。
- 解决Vue无法监听到对象新增的属性的问题；

**参考文章**

- [Vue3 为什么要用 Proxy 代替 Object.defineProperty 实现响应式](https://www.jianshu.com/p/8cde476238f0)



# 3.async和defer的区别 ?

## 3.1 页面的加载和渲染过程

1. 浏览器通过HTTP协议请求服务器，获取HMTL文档并开始从上到下解析，构建DOM；
2. 在构建DOM过程中，如果遇到外联的样式声明和脚本声明，则暂停文档解析，创建新的网络连接，并开始下载样式文件和脚本文件；
3. 样式文件下载完成后，构建CSSDOM；脚本文件下载完成后，解释并执行，然后继续解析文档构建DOM；
4. 完成文档解析后，将DOM和CSSDOM进行关联和映射，最后将视图渲染到浏览器窗口 。

在这个过程中，脚本文件的下载和执行是与文档解析同步进行，也就是说，它会阻塞文档的解析。

## 3.2 async

1.加载JS不阻塞DOM渲染过程，执行JS会阻塞DOM渲染过程。
2.一定会在load事件之前执行，可能会在DOMContentLoaded之前或之后执行。

情况1： HTML 还没有被解析完的时候，async脚本已经加载完了，那么 HTML 停止解析，去执行脚本，脚本执行完毕后触发DOMContentLoaded事件。如下图所示：
![image-20220104162634305](.\JS部分.assets\image-20220104162634305.png)
情况2： HTML 解析完了之后，async脚本才加载完，然后再执行脚本，那么在HTML解析完毕、async脚本还没加载完的时候就触发DOMContentLoaded事件。如下图所示：
![image-20220104162657199](.\JS部分.assets\image-20220104162657199.png)
总之， DomContentLoaded 事件只关注 HTML 是否被解析完，而不关注 async 脚本。

## 3.3 defer

1.加载JS不阻塞DOM渲染过程，JS脚本会等到DOM渲染完成后才会执行。
2.DOMContentLoaded 只有在 defer 脚本执行结束后才会被触发。

情况1：HTML还没解析完成时，defer脚本已经加载完毕，那么defer脚本将等待HTML解析完成后再执行。defer脚本执行完毕后触发DOMContentLoaded事件。如下图所示
![image-20220104162715624](.\JS部分.assets\image-20220104162715624.png)
情况2：HTML解析完成时，defer脚本还没加载完毕，那么defer脚本继续加载，加载完成后直接执行，执行完毕后触发DOMContentLoaded事件。如下图所示：
![image-20220104162726574](.\JS部分.assets\image-20220104162726574.png)

《JavaScript高级程序设计》一书的说法是:“按照h5规范，两个defer脚本会安装它们出现的先后顺序执行，两个脚本会在DOMContentLoaded之前执行。但事实上，defer脚本不一定会按顺序执行，也不一定会在DOMContentLoaded之前执行。”

**参考文章**

- [async、defer与DOMContentLoaded的执行先后关系](https://blog.csdn.net/zyj0209/article/details/79698430)
- [defer和async的区别](https://www.jianshu.com/p/c7c331ea4fe8)



# 4.JS垃圾回收机制

## 4.1 垃圾回收机制

**JavaScript 具有自动垃圾收集机制**（GC：GarbageCollecation），也就是说，执行环境会负责管理代码执行过程中使用的内存。开发人员不用再关心内存使用问题，所需内存的分配以及无用内存的回收完全实现了自动管理。

## 4.2 内存生命周期

JS环境中分配的内存一般有如下生命周期：

1. 内存分配：当我们申明变量、函数、对象,并执行的时候，系统会自动为他们分配内存
2. 内存使用：即读写内存，也就是使用变量、函数等
3. 内存回收：使用完毕，由垃圾回收机制自动回收不再使用的内存

## 4.3 垃圾回收机制策略

### **4.3.1 标记清除算法**

JavaScript 中最常用的垃圾收集方式是标记清除（mark-and-sweep）。

这个算法把“对象是否不再需要”简化定义为“对象是否可以获得”。

该算法假定设置一个叫做根（root）的对象（在Javascript里，根是全局对象）。垃圾回收器将**定期从根开始**（在JS中就是全局对象）扫描内存中的对象。凡是能从根部到达的对象，都是还需要使用的。那些无法由根部出发触及到的对象被标记为不再使用，稍后进行回收。

**何时开始垃圾回收?**
通常来说，在使用标记清除算法时，未引用对象并不会被立即回收。取而代之的做法是，垃圾对象将一直累计到内存耗尽为止。**当内存耗尽时，程序将会被挂起，垃圾回收开始执行。**

**标记清除算法缺陷**

- 那些无法从根对象查询到的对象都将被清除
- 垃圾收集后有可能会造成大**量的内存碎片**

### 4.3.2 **引用计数算法**

此算法把“对象是否不再需要”简化定义为“**对象有没有其他对象引用到它**”。如果没有引用指向该对象（零引用），对象将被垃圾回收机制回收。

**引用计数**的含义是跟踪记录每个值被引用的次数。当声明了一个变量并将一个引用类型值赋给该变量时，则这个值的引用次数就是1。如果同一个值又被赋给另一个变量，则该值的引用次数加1。相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数减1。当这个值的引用次数变成0时，则说明没有办法再访问这个值了，因而就可以将其占用的内存空间回收回来。

**引用计数缺陷**

该算法有个限制：无法处理循环引用。如果两个对象被创建，并互相引用，形成了一个循环。它们被调用之后会离开函数作用域，所以它们已经没有用了，可以被回收了。然而，引用计数算法考虑到它们互相都有至少一次引用，所以它们不会被回收。

### 4.3.3 Chrome V8 垃圾回收算法

V8 引擎采用的是分代式垃圾回收机制。目的是通过区分「临时」与「持久」对象；多回收「临时对象区」（**新生代**young generation），少回收「持久对象区」（**老生代** tenured generation），减少每次需遍历的对象，从而减少每次GC的耗时。

**V8堆的整体大小就是新生代所用内存空间加上老生代的内存空间。**如下图：

![image-20220105175758859](.\JS部分.assets\image-20220105175758859.png)

#### **V8新生代算法（Scavenge）**

**晋升:**

对象从新生代中移动到老生代中的过程称为晋升。

**晋升条件:**

1. 对象是否经历过Scavenge回收。
2. 一个是To空间的内存占用比超过25%限制。

**算法过程：**

将新生代内存平分为**From空间（处于使用状态的空间）、To空间（处于闲置状态的空间）**。分配对象时，现在From空间分配。开始进行垃圾回收时，会检查From空间中的存活对象。**如果达到晋升条件的对象，会移动到老生代中，完成晋升。**将这些存活对象复制到To空间中，而(From空间内的)非存活对象占用的空间将会被释放。完成复制后，From空间和To空间的角色发生对换(**即以前的From空间释放后变为To;To空间在复制存活的对象后,变为From空间**)。简而言之，在垃圾回收过程中，就是通过将存活对象在两个半空间之间进行复制。

**Scavenge算法缺点:**
**只能使用堆内存中的一半**，这是由划分空间和复制机制所决定的。

**Scavenge算法优点：**

1. 只处理新生代存活的对象（命周期短的场景存活对象只占少部分），**时间效率高**。

#### **V8老生代算法（Mark-Sweep && Mark-Compact）**

**Mark-Sweep:**

Mark-Sweep是标记清除的意思，它分为标记和清除两个阶段。**Mark-Sweep在标记阶段遍历堆中所有对象，并标记活着的对象，在随后的清除阶段中，只清除没有被标记的对象。**

**Mark-Sweep的缺点:**

标记清除完成后，内存空间会出现不连续的状态（**内存碎片**）。

**Mark-Compact:**

为了解决Mark-Sweep的内存碎片问题，Mark-Compact被提出来。Mark-Compact是标记整理的意思。**在标记清除的过程中，将活着的对象往一端移动，移动完成后，直接清理掉边界外的内存。**完成移动后，就可以直接清除最右边的存活对象后面的内存区域完成回收。

![image-20220105175717945](.\JS部分.assets\image-20220105175717945.png)

#### **增量式标记回收(Incremental Marking)**

**垃圾回收的3种基本算法都需要将应用逻辑暂停下来**，待执行完垃圾回收后再恢复执行应用逻辑，这种行为被称为“全停顿”（stop-the-world）。

增量式标记回收是**拆分为许多小“步进”，每做完一“步进”就让js应用逻辑执行一小会，垃圾回收与应用逻辑交替执行直到标记阶段完成。**

**优点：**

- V8在经过增量标记的改进后，垃圾回收的最大停顿时间可以减少到原本的1/6左右。

**参考文章**

- [前端面试查漏补缺--(二) 垃圾回收机制](https://juejin.cn/post/6844903781079973902#heading-13)



# 5.什么是 Eventloop ？

JS是一门单线程的非阻塞的脚本语言，主要执行环境是浏览器。后来H5出现Web Worker的多线程，但是新线程都完全受主线程控制，不能独立执行。

非阻塞的特点主要是因为Event Loop（事件循环）。

代码自上而下执行，代码在执行时会被压入`执行栈（stack）`中，当遇到异步任务（DOM的点击事件、Ajax请求、setTimeOut函数）时，会交给Web API来维护，当异步任务执行完会将对应的回调函数推入事件队列（Event Queue）中，当执行栈中任务全部执行完，浏览器会读取任务队列，并将回调函数压入执行栈中，然后循环执行。这就是事件循环（Event Loop）。

## 5.1 异步任务类型

- macro-task(宏任务)：包括整体代码script，setTimeout，setInterval
- micro-task(微任务)：Promise.resolve().then，process.nextTick

不同类型的任务会进入对应的Event Queue，比如`setTimeout`和`setInterval`会进入宏任务Event Queue。

## 5.2 循环流程

进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。



# 6.async/await 原理是什么？

## 6.1 概念：

`async/await`其实是`Promise`的语法糖，它是为优化then的嵌套调用而开发出来的。从字面上来看，async是“异步”的简写，await译为等待，所以我们很好理解async声明function是异步的，await等待是一个`Promise`对象，或者是`其他任何值`，并且await会暂停当前`async function`的执行，等待Promise的处理完成。

`async`声明的异步函数，把return后面的值通过Promise.resolve()返回Promise对象。

`await`如果等待的是Promise对象，则返回Promise的处理结果；如果是其他值，则返回该值本身。若Promise正常处理（fulfillded），其将回调的resolve函数参数作为await表达式的值；若Promise处理异常（rejected），await表达式会把Promise异常原因抛出，await表达式的值为undefined，并且await表达式后面的代码被暂停了；

## 6.2 实现原理

`async/await`是一种`语法糖`，用到的是ES6里的`迭代函数——generator函数`

### 6.2.1 generator函数

`generator函数`跟普通函数在写法上的区别就是，多了一个星号`*`，并且只有在`generator函数`中才能使用`yield`，什么是`yield`呢，他相当于`generator函数`执行的`中途暂停点`，比如下方有3个暂停点。而怎么才能暂停后继续走呢？那就得使用到`next方法`，`next方法`执行后会返回一个对象，对象中有`value 和 done`两个属性

- value：暂停点后面接的值，也就是yield后面接的值
- done：是否generator函数已走完，没走完为false，走完为true

```javascript
function* gen() {
  yield 1
  yield 2
  yield 3
}
const g = gen()
console.log(g.next()) // { value: 1, done: false }
console.log(g.next()) // { value: 2, done: false }
console.log(g.next()) // { value: 3, done: false }
// 可以看到最后一个是undefined，这取决于你generator函数有无返回值
console.log(g.next()) // { value: undefined, done: true }

```

### 6.2.2 实现async/await

```javascript
// 模拟异步操作
function fn(nums) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(nums * 2)
        }, 1000)
    })
}
// 生成器函数
function* gen() {
    console.time('test')
    const num1 = yield fn(1)
    const num2 = yield fn(num1)
    const num3 = yield fn(num2)
    console.timeEnd('test')
    return num3
}
function generatorToAsync(generatorFn) {
    return function () {
        // generatorFn有可能传参
        const gen = generatorFn.apply(this.arguments)
        // 模拟async函数返回一个Promise对象
        return new Promise((resolve, reject) => {
            // 定义一个go函数，用来自动执行 gen.next() 方法
            function go(key, arg) {
                let res
                try {
                    // 相当于res = gen.next('参数')
                    res = gen[key](arg) // 这里有可能会执行返回reject状态的Promise
                } catch (error) {
                    return reject(error) // 报错的话会走catch，直接reject
                }

                // 解构获得value和done
                const { value, done } = res
                if (done) {
                    // 如果done为true，说明走完了，进行resolve(value)
                    return resolve(value)
                } else {
                    // 如果done为false，说明没走完，还得继续走

                    // value有可能是：常量、Promise，Promise有可能是成功或者失败
                    return Promise.resolve(value).then(val => go('next', val), err => go('throw', err))
                }

            }
            go('next')// 第一次执行
        })
    }
}
// 测试代码
const genToAsync = generatorToAsync(gen)// 生成async函数构造器函数
const asyncRes = genToAsync()// 生成async函数
console.log(asyncRes);// async函数返回的是Promise
asyncRes.then(res=>{
    // test: 3032.6279296875 ms
    console.log(res);// 3秒后输出8
})
```

**参考文章**

- [【学习笔记】深入理解async/await](https://www.cnblogs.com/youma/p/10475214.html)
- [7张图，20分钟就能搞定的async/await原理！为什么要拖那么久？](https://juejin.cn/post/7007031572238958629#heading-12)



# 7.weak-Set、weak-Map 和 Set、Map 区别

## 7.1 Set

Set是无重复值的有序列表，根据`Object.is()`来判断内部值的不相等。

**常见API：**

- set.add(xxx) 新增
- set.get(xxx) 获取
- set.delete(xxx) 删除
- set.has(xxx) 判断是否存在
- set.clear() 清除数据
- set.size 获取数值的个数
- set.forEach 遍历set

## 7.2 WeakSet

`WeakSet`拥有了大部分Set有的API，除了forEach、size、clear等等。

## 7.3 Map

`Map`是无重复键名的，存放键值对的数据结构，键的去重是根据`Object.is()`来判断的，键名和键值是任意数据类型。

**常见API：**

- map.set('key','value') 新增
- map.get('key') 获取
- map.delete('key') 删除
- map.has('key') 判断是否存在
- map.clear() 清除数据
- set.size 获取数值的个数
- set.forEach 遍历map

## 7.4 WeakMap

`WeakMap`拥有了大部分Map有的API，除了forEach、size、clear等等。

**区别：**

1. `Set`和`Map`的API传参可以是任意数据类型的值，`WeakSet`API传参和`WeakMap`的key只能是对象类型的值，否则会报错；
2. `Set`在存放对象和`Map`的key为对象，存放的是对象的引用，所存储的对象赋值为`null`时，如果`Set`实例还存在，该对象无法被`GC`回收，从而无法释放内存；`WeakSet`在存放对象时和`WeakMap`的key为对象，存放的是对象的弱引用；不影响`GC`回收；
3. `WeakSet`在存放对象时，存放的是对象的弱引用，如果`WeakSet`实例还存在，该对象可以被`GC`回收；
4. `Set`和`Map`可迭代；`WeakSet`和`WeakMap`不可迭代，无法用于`for-of`循环，无法暴露出任何迭代器（例如 keys() 与 values()方法）;
5. `WeakSet`和`WeakMap`没有**forEach方法** 、**size属性** 、**clear方法**；

**参考文章**

- [ES6新特性-Set、Map、Weak Set、Weak Map](https://juejin.cn/post/6973246687020056590)

# 8.Promise 原理

**Promise** 是异步编程的一种解决方案： 从语法上讲，promise是一个对象，从它可以获取异步操作的消息；从本意上讲，它是承诺，承诺它过一段时间会给你一个结果。 promise有三种状态：**pending(等待态)，fulfiled(成功态)，rejected(失败态)**；状态一旦改变，就不会再变。创建promise实例后，它会立即执行。

## 8.1 解决问题

promise是用来解决两个问题的：

- 回调地狱，代码难以维护， 常常第一个的函数的输出是第二个函数的输入这种现象
- promise可以支持多个并发的请求，获取并发请求中的数据
- 这个promise可以解决异步的问题，本身不能说promise是异步的

## 8.2 方法

Promise是一个构造函数，自己身上有all、reject、resolve这几个方法，原型上有then、catch等方法。

## 8.3 手写一个Promise

```javascript
class MyPromise {
  // 构造方法
  constructor(executor) {
    // 初始化值
    this.initValue()
    // 初始化this指向
    this.initBind()
    // Promise中有throw的话，就相当于执行了reject。这就要使用try catch了
    try {
      // 执行传进来的函数
      executor(this.resolve, this.reject)
    } catch (error) {
      // 捕捉到错误直接执行reject
      this.reject(error)
    }
  }

  initValue() {
    // 初始化值
    this.PromiseResult = null // 最终的值
    this.PromiseState = 'pending' // 状态
    this.onFulfilledCallbacks = [] // 保存成功回调
    this.onRejectedCallbacks = [] // 保存失败回调
  }
  initBind() {
    // 初始化this
    this.resolve = this.resolve.bind(this)
    this.reject = this.reject.bind(this)
  }
  resolve(value) {
    // state是不可变的
    if (this.PromiseState != 'pending') { return }
    // 如果执行resolve，状态变为fulfilled
    this.PromiseState = 'fulfilled'
    // 终值为传进来的值
    this.PromiseResult = value
    // 执行保存的成功回调
    while (this.onFulfilledCallbacks.length) {
      this.onFulfilledCallbacks.shift()(this.PromiseResult)
    }
  }
  reject(reason) {
    // state是不可变的
    if (this.PromiseState != 'pending') { return }
    // 如果执行reject，状态变为rejected
    this.PromiseState = 'rejected'
    // 终值为传进来的reason
    this.PromiseResult = reason
    // 执行保存的失败回调
    while (this.onRejectedCallbacks.length) {
      this.onRejectedCallbacks.shift()(this.PromiseResult)
    }
  }
  then(onFulfilled, onRejected) {
    // 接收两个回调 onFulfilled, onRejected

    // 参数校验，确保一定是函数
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : val => val
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason }

    // 封装一个thenPromise
    var thenPromise = new MyPromise((resolve, reject) => {

      const resolvePromise = cb => {
        setTimeout(() => {
          try {
            const x = cb(this.PromiseResult)
            if (x === thenPromise) {
              // 不能返回自身
              throw new Error('不能返回自身')
            }
            if (x instanceof MyPromise) {
              // 如果返回值是Promise
              // 如果返回值是promise对象，返回值为成功，新promise就是成功
              // 如果返回值是promise对象，返回值为失败，新promise就是失败
              // 谁知道返回的promise是失败成功？只有then知道
              x.then(resolve, reject)
            } else {
              // 非Promise就直接成功
              resolve(x)
            }
          } catch (error) {
            // 处理报错
            reject(error)
            throw new Error(error)
          }
        }, 0)
      }

      if (this.PromiseState === 'fulfilled') {
        // 如果当前是成功状态，执行第一个回调
        resolvePromise(onFulfilled)
      } else if (this.PromiseState === 'rejected') {
        // 如果当前是失败状态，执行第二个回调
        resolvePromise(onRejected)
      } else if (this.PromiseState === 'pending') {
        // 如果状态为待定状态，暂时保存两个回调
        this.onFulfilledCallbacks.push(resolvePromise.bind(this, onFulfilled))
        this.onRejectedCallbacks.push(resolvePromise.bind(this, onRejected))
      }

    })
    // 返回这个包装的Promise
    return thenPromise
  }

  /**
   * all
   * 接收一个Promise数组，数组中如有非Promise项，则此项当做成功
   * 如果所有Promise都成功，则返回成功结果数组
   * 如果有一个Promise失败，则返回这个失败结果
   * @param {Array} promise
   * @returns
   */
  static all(promise) {
    const result = []
    let count = 0
    return new MyPromise((resolve, reject) => {
      const addData = (index, value) => {
        result[index] = value
        count++
        if (count === result.length) {
          resolve(result)
        }
      }
      promise.forEach((promise, index) => {
        if (promise instanceof MyPromise) {
          promise.then(res => {
            addData(index, res)
          }, err => reject(err))
        } else {
          addData(index, promise)
        }
      })
    })
  }

  /**
   * race
   * 接收一个Promise数组，数组中如有非Promise项，则此项当做成功
   * 哪个Promise最快得到结果，就返回那个结果，无论成功失败
   * @param {Array} promise
   * @returns
   */
  static race(promise) {
    return new MyPromise((resolve, reject) => {
      promise.forEach((promise, index) => {
        if (promise instanceof MyPromise) {
          promise.then(res => {
            resolve(res)
          }, err => {
            reject(err)
          })
        } else {
          resolve(promise)
        }
      })
    })
  }

  /**
   * allSettled
   * 接收一个Promise数组，数组中如有非Promise项，则此项当做成功
   * 把每一个Promise的结果，集合成数组，返回
   * @param {Array} promise
   * @returns
   */
  static allSettled(promise) {
    const result = []
    let count = 0
    return new MyPromise((resolve, reject) => {
      const addData = (status, value, i) => {
        result[i] = {
          status,
          value
        }
        count++
        if (count === result.length) {
          resolve(result)
        }
      }
      promise.forEach((promise, index) => {
        if (promise instanceof MyPromise) {
          promise.then(res => {
            addData('fulfilled', res, index)
          }, err => {
            addData('rejected', err, index)
          })
        } else {
          addData('fulfilled', promise, index)
        }
      })
    })
  }

  /**
   * any
   * any与all相反
   * 接收一个Promise数组，数组中如有非Promise项，则此项当做成功
   * 如果有一个Promise成功，则返回这个成功结果
   * 如果所有Promise都失败，则报错
   * @param {Array} promise
   * @returns
   */
  static any(promise){
    return new MyPromise((resolve,reject)=>{
      let count = 0
      promise.forEach((promise,index)=>{
        promise.then(res=>{
          resolve(res)
        },err=>{
          count++
          if(count === promise.length){
            reject(new AggregateError('All promises were rejected'))
          }
        })
      })
    })
  }
}
```



# 9.setTimeout、setInterval、requestAnimationFrame 各有什么特点？

`setTimeout`，`setInterval`在 `event loop` 的宏任务中，当主线程结束时才会按照任务队列加载，因为它们需要在主线程任务和微任务结束后执行，所以时间计算有偏差。

`requestAnimationFrame` 在主线程中执行，所以更加准确，以1秒钟60次（大约每16.7毫秒一次）的频率执行。



# 10.new操作符的实现原理

> new运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象

**举个例子**

```javascript
// Otaku 御宅族，简称宅
function Otaku(name, age) {
    this.name = name
    this.age = age
}
// 重量
Otaku.prototype.weight = 80
Otaku.prototype.sayHi = function () {
    console.log(`I am ${this.name},age is ${this.age}.`);
}
let person = new Otaku("张三", 12)
console.log(person.name);//张三
console.log(person.age);//12
console.log(person.weight);//80
person.sayHi();// I am 张三,age is 12.
```

从这个例子中，我们可以看到person实例可以：

1. 访问到 Otaku 构造函数的属性；
2. 访问到 Otaku.prototype 中的属性或方法；

> 注意：加入构造函数有返回值，并且返回值是`对象`，则new操作符会返回该`对象`

```javascript
// Otaku 御宅族，简称宅
function Otaku(name, age) {
    this.name = name
    this.age = age
    return{
        name:"哈哈",
        age:22
    }
}
// 重量
Otaku.prototype.weight = 80
Otaku.prototype.sayHi = function () {
    console.log(`I am ${this.name},age is ${this.age}.`);
}
let person = new Otaku("张三", 12)
console.log(person.name);//哈哈
console.log(person.age);//22
console.log(person.weight);//undefined
person.sayHi();//报错
```

## 10.1 实现一个new函数：

```javascript
function objectFactory() {
    let obj = new Object()
    Constructor = Array.prototype.shift.call(arguments)// 将构造函数取出，arguments剩余的数据为传入的参数
    // 将原型指向obj
    obj.__proto__ = Constructor.prototype
    let result = Constructor.apply(obj, arguments)
    // 如果构造函数的返回值不为对象，则返回obj。否则返回该对象result
    return Object.prototype.toString.apply(result).includes("Object") ? result : obj
}
```

**测试**

```javascript
// Otaku 御宅族，简称宅
function Otaku(name, age) {
    this.name = name
    this.age = age
}
// 重量
Otaku.prototype.weight = 80
Otaku.prototype.sayHi = function () {
    console.log(`I am ${this.name},age is ${this.age}.`);
}
// let person = new Otaku("张三", 12)
let person = objectFactory(Otaku, "张三", 12)
console.log(person.name);//张三
console.log(person.age);//12
console.log(person.weight);//80
person.sayHi();//I am 张三,age is 12.
```

**参考链接**

- [JavaScript深入之new的模拟实现](https://github.com/mqyqingfeng/Blog/issues/13)

