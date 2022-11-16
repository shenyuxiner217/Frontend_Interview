# Frontend-Interview

## Javascript

* isNaN & Number.isNaN

  首先我们不能用==或者===（equality operators）to compare a value against NaN来判断一个值是不是NaN，因为NaN == NaN及NaN === NaN都返回false。

  isNaN: 含义是：is this value, when coerced to a numeric value, an IEEE-754 'Not A Number' value? 会有一个implicit conversion to number。
  你只能得到true当：isNaN(undefined), isNaN({}), isNaN(NaN), isNaN('char')，空字符串，空格字符串，空数组，null，boolean都可以得到false。

  Number.isNaN: determines whether the passed value is NaN and its type is Number, no implict conversion. 只有当值为number类型（NaN也是number类型）并且不是一个数字时，才会返回true。只要本身类型不是数字 || 是一个合法的数字，都会返回false。
  因为各种字符串都不是number type，因此都会返回false。

  Number.isNaN(0/0)
  1/0 === Infinity( typeof number)
  
  
* prototype对象和原型链

  每个实例对象身上都有一个隐藏的[[prototype]]属性，可以指向另一个对象或者null，这个对象就是原型对象。
  当一个对象的原型属性指向另一个新对象时，他会继承新对象的所有属性和方法，而这个新对象的原型属性再指向另一个对象，最后指向Object.prototype，即所有对象原型属性的终点。当我们调用第一个对象的属性和方法时，如果该对象身上没有这个属性或方法，则会顺着原型属性向上一层找，一直找到最后一层，然后调用它。形成了一个链式的关系。
  
* 闭包
  
  所有函数都有一个隐藏的[[Environment]]属性，该属性保存了对创建该函数位置的外部环境的引用，即可以记住并访问创建该函数位置外部的环境变量，与函数在哪里调用无关。
  
  特点：
  - 函数嵌套函数。
  - 函数内部可以引用外部的参数和变量。
  - 参数和变量不会被垃圾回收机制回收。
  
  优点：
  1. 延长一些变量的生命周期，长期存在与内存中
  2. 避免全局污染
  3. 封装，私有成员的存在
  4. 使外部也可以操作局部变量

  应用：
  1. 回调函数
  2. 自调用函数封装私有方法，可以创建单例模式
  3. 函数柯里化
  4. 防抖和节流的应用

  缺点：
  1. 占用的内存没有及时释放，长期的积累会导致溢出
  
  解决办法：
  1. 手动去释放内存，让内部函数的引用指向null
  2. 尽量不使用闭包
  
* JS的继承

  包含哪几种继承方式：
  
  原型继承：JS中，A类继承B类，是通过原型实现的。extends语法事实上创建了两个[[prototype]]的引用，首先让A类通过原型链指向B类，继承了B类的所有属性，其次A类的prototype属性通过原型链指向B类的prototype，继承了B类prototype中的所有方法。
  

  
  
* call & apply & bind
  
  func.call(obj, arg1, arg2, ...) 将obj设置为this，将参数以参数列表的形式传给func并调用
  
  func.apply(obj, [args]) 将obj设置为this，将参数以数组的形式传递给func并调用
  
  func.bind(obj, arg1, arg2, ...) 将this指向obj，并传入参数，会返回一个新的函数，可以在任何地方调用但是不丢失this
  
  
  
* 判断是数组还是对象

  0. typeof不行！！
  1. a instanceof Array
  2. a.constructor === Array
  
  以上两种方法的弊端：
  用以上两种方式判断是否是array的时候，会忽略iframe元素
  
  3. Array.isArray() // 兼容性问题
  4. Object.prototype.toString().call(a) === "[object Array]" （推荐）
  
  
  
## DOM & BOM

* defer 和 aync

  浏览器遇到script标签会停止构建DOM转而开始加载+运行script里的脚本文件，一方面会影响页面的加载速度，一方面会导致一些还未构建的DOM无法被脚本操作。如果将脚本文件放在最后，则需要等待整个html文件加载完成才开始加载+执行。因此我们需要在script中使用属性defer或者async。注意：两者都要有外部src文件才起作用，否则被忽略。
  
  两者都会让浏览器不要等待脚本，继续处理HTML，构建DOM，页面的内容会显示。而脚本会进行异步加载。
  
  defer脚本加载完后会等待页面页面解析完成，DOMContentLoaded事件之前执行脚本，并且脚本是会按照顺序执行的。因此defer用于需要整个DOM的脚本，或者脚本的执行顺序很重要的情况下。
  
  async表示异步加载完成后立刻执行，是一个完全独立的脚本，它不会等待页面其他内容或者脚本执行，其他内容也不会等待它执行，包括DOMContentLoaded。因此无法决定它的执行时间，执行顺序也是按照加载优先顺序的。因此常用于完全独立于页面的一些外部脚本。
  
  还有一种动态脚本：使用 JavaScript 动态地创建一个脚本，并将其附加（append）到文档（document）中：
  ```javascript
  let script = document.createElement('script')
  script.scr = './xx.js'
  document.body.append(scrpit)
  ```
  这种方式下默认为async，除非将scrpit.async = false设置后，会变成defer。
  
  

## ES6

* 分别暴露，统一暴露，默认暴露
  分别暴露：在需要暴露的变量或方法前加export
  统一暴露：在js文件最后加上 export {variables, functions};
  默认暴露：export default，一个js文件里只能用一次



## HTML && CSS

* 伪类与伪元素
  
  伪类：实际不存在的类，我们希望不通过静态的标记元素，而是动态的选中某些状态下的某些元素。以单个冒号开头。
  伪元素：实际不存在的元素，表示页面上的某些位置的元素。以两个冒号开头。

* rem 移动端适配
  将html根元素字体大小设置为屏幕宽度，即1rem
  
* BFC 块级格式化上下文 (Block Formatting Context)

  一个环境中的元素不会影响到其它环境中的布局，BFC就是一个作用范围，把它理解成是一个独立的容器，并且这个容器里box的布局与这个容器外的box毫不相干。
  
  触发条件：
  1. 浮动元素
  2. position为fixed或者absolute
  3. overflow不是visible
  4. 弹性盒
  5. 内联块 inline-block

  解决问题
  1. 清除
  5. 内联块 inline-block
  

## HTTP

* POST和GET的区别

* TCP vs UDP

* url输入后发生了什么

  1. 解析域名：先查看本地是否有该域名对应的IP地址，有的话则直接发送请求，没有的话则会发送一个DNS请求到DNS服务器，服务器会把域名和对应的IP地址返回给用户。
  2. 哪都对应的IP地址后，浏览器会以一个随机的端口向服务器的web程序发起TCP连接请求，这个请求通过各种路由到达服务器端后，再由TCP/IP协议栈层层解包到达web程序。
  3. 通过三次握手后，客户端与服务器端建立起了TCP连接，发起一个HTTP请求，一个请求报文辉包含请求行、请求头、请求体。
  4. 服务器端收到请求后返回HTTP响应报文，也包含了响应行、响应头、响应体。
  5. 浏览器得到请求返回的HTML文档时，浏览器的渲染引擎开始工作，主线程会自上而下解析html文件，通过html构建dom树、通过css文件构建render树、通过计算去布局render树、绘制render树。
  6. 在解析过程中遇到请求外部资源时，过程是异步的不会影响html文档加载。而加载过程中遇到js文件，html文档的渲染会被挂起，因为js可能会修改dom。因此我们常常把js放在文档最后。
  7. 当遇到外部静态资源时，浏览器会再次发送请求，并且可以对这些资源进行缓存。
  8. 一个完整的html页面就渲染完成了。


* 缓存
  
  缓存分为浏览器缓存（localstorage和cookie）和HTTP缓存
  HTTP缓存：分为强制缓存和协商缓存
    强制缓存：如果浏览器判断请求的目标资源有效命中强缓存，则无需与服务端做通讯直接从内存中读取资源
            expires：在响应头该字段设置expires时间实现强缓存，但是expires过度依赖本地时间，因此已经被废弃
            cache-control：在响应头设置cache-control实现强缓存，通过max-age来设置强制缓存的时长；no-cache是强制尽情协商缓存，no-store是禁止所有缓存策略
    协商缓存：基于last-modified的协商缓存：需要在服务器端读出文件修改时间，并且把时间赋给响应头的last-modified字段，并设置cache-control:no-cache;
            当客户端读取到last-modified的时候，会在下次请求头中携带一个字段：if-modified-since: 从last-modified中读取的时间；服务器每次拿到资源后会对比时间看是否要返回新资源
            基于etag的协商缓存：根据文件内容计算出唯一的哈希值，服务器会把它放在响应头的etag字段返回给用户，用户每次请求时将etag的值放入请求头的if-none-match中发送给服务器
            服务器进行对比，如果和目标资源的etag完全吻合则返回304和空的响应体，如果不吻合则返回新的文件和新的响应头中的etag给客户端
             

## REACT

* React的理解

  React 是一个网页 UI 框架，通过 组件化 的方式解决 视图层 开发 复用 的问题。它的核心设计思路有三点，分别是声明式、组件化与 通用性。
  声明式：优势在于直观与组合
  组件化：优势在于视图的拆分与模块复用，实现了高复用低耦合
  通用性：在于一次学习，随处编写，比如 React Native，React 360 
  
* 虚拟DOM

  通过JS对象来模拟DOM中的节点，减少对于真实DOM的操作，因为操作JS对象的性能会比操作真实DOM要高很多；当状态发生变更时，将变更前后的虚拟 DOM 树进行差异比较，这个过程称为 diff，diff算法能够大大提升渲染性能，而diff算法也是基于虚拟DOM的高效算法。生成的结果称为 patch，即需要更新的DOM。计算之后，会渲染 Patch 完成对真实 DOM 的操作。

* Fiber

  每当我们触发一次组件的更新，React 都会构建一棵新的虚拟 DOM 树，通过与上一次的虚拟 DOM 树进行 diff，这个过程是个递归的过程。同步渲染的递归调用栈是非常深的，只有最底层的调用返回了，整个渲染过程才会开始逐层返回。而一旦渲染占用了主线程，则完成之前用户无法进行任何操作。Fiber 会将一个大的更新任务拆解为许多个小任务。每当执行完一个小任务时，渲染线程都会把主线程交回去，看看有没有优先级更高的工作要处理。这就是所谓的异步渲染。
  Fiber 架构的重要特征就是可以被打断的异步渲染模式。React 16 的生命周期被划分为了 reconciliation 和 commit 两个阶段，而 commit 阶段又被细分为了 pre-commit 和 commit。reconciliation 阶段在执行过程中允许被打断，而 commit 阶段则总是同步执行的。因为reconciliation阶段用户无法感知，而commit 阶段的操作则涉及真实 DOM 的渲染。
  
* React渲染页面的两个阶段
  调度阶段（reconciliation）：在这个阶段 React 会更新数据生成新的 Virtual DOM，然后通过Diff算法，快速找出需要更新的元素，放到更新队列中去，得到新的更新队列。
      更新 state 与 props；
      调用生命周期钩子；
      生成 virtual dom
      这里应该称为 Fiber Tree 更为符合；
      通过新旧 vdom 进行 diff 算法，获取 vdom change
      确定是否需要重新渲染
  渲染阶段（commit）：这个阶段 React 会遍历更新队列，将其所有的变更一次性更新到DOM上
  
* 函数式组件 vs 类式组件

  类组件和函数组件之间的差异，是面向对象和函数式编程这两套不同的设计思想之间的差异。React 组件本身的定位就是函数，进数据、出UI的函数。作为开发者，我们编写的是声明式的代码。
  性能优化上，类组件主要依靠 shouldComponentUpdate 阻断渲染来提升性能，而函数组件依靠 React.memo 缓存渲染结果来提升性能。
  类组件在未来时间切片与并发模式中，由于生命周期带来的复杂度，并不易于优化。函数组件本身轻量简单，且在 Hooks 的基础上提供了比原先更细粒度的逻辑组织与复用。
  

* 高阶组件
  
  高阶组件（HOC，Higher-Order Components）不是组件，而是一个函数，它会接收一个组件作为参数并返回一个经过改造的新组件。实现方式有属性代理和反向继承。
  属性代理：适用于强化props，条件渲染，组件懒加载
  ```javascript
  function hoc(Child) {
      return class Parent extends React.Component {
          state = {...}
          render() {
              return (<Child {...this.props} {...this.state}>) 
          }
      }
  }
  ```
  反向继承：直接获取组件状态，绑定事件，劫持渲染，控制生命周期
  ```javascript
  function hoc(ComponentA) {
      return class ComponentB extends ComponentA {
      }
  }
  
  ```
  
  用处：
    - 抽取重复代码，实现组件复用，常见场景：页面复用。
    - 条件渲染，控制组件的渲染逻辑（渲染劫持），常见场景：权限控制。
    - 捕获被处理组件的生命周期，常见场景：组件渲染性能追踪、日志打点。

  
  优点：
    1. 复用逻辑：高阶组件更像是一个加工react组件的工厂，批量对原有组件进行加工，包装处理。
    2. 强化props：这个是HOC最常用的用法之一，高阶组件返回的组件，可以劫持上一层传过来的props,然后混入新的props,来增强组件的功能。代表作react-router中的withRouter。
    3. 控制渲染：劫持渲染是hoc一个特性，在wrapComponent包装组件中，可以对原来的组件，进行条件渲染，节流渲染，懒加载等功能

  


* React组件渲染性能优化
  React中某个组件变化时，需要遍历从root到叶子组件整个路径上的所有组件进行比较并调用render方法，只有对该组件内部才会做diff算法，尽管其他组件没有发生变化。
  
  1. 将变得部分和不变的部分分离，抽成单独的组件。
  2. 类组件通过控制shouldComponentUpdate来控制，而函数式组件通过memo和useMemo
  3. purecomponent

* Memo vs useMemo

  React.memo() 是一个高阶组件 (HOC)，它接收一个组件A作为参数并返回一个组件B，如果组件B的 props（或其中的值）没有改变，则组件 B 会阻止组件 A 重新渲染。
  React.useMemo() 是一个hook，“创建”一个依赖函数，精细化控制是否需要重复执行某一段逻辑函数，当其中一个依赖项更改时， useMemo重新计算记忆的值，而不需要在每个渲染进行昂贵的计算。内部引用的每个值也应该出现在依赖项数组中。
  
  
* useMemo vs useEffect

  useMemo -> render -> useEffect
  



* Hooks

  一系列以 “use” 作为开头的方法，它们提供了让你可以完全避开 class式写法，在函数式组件中完成生命周期、状态管理、逻辑复用等几乎全部组件开发工作的能力。
  规范：
    只在 React 函数组件中调用 Hook，而不在普通函数中调用 Hook。
    只在最顶层使用 Hook，而不要在循环，条件或嵌套函数中调用 Hook。
   
   1. 分散在各种声明周期里的代码块，通过 Hooks 的方式将 相关的内容 聚合到一起，提升阅读性。
   2. 比class组件更轻量级，更不容易出错，比如this的指向问题，降低代码bug量。
   3. 自定义hook组件完成逻辑复用
    
    
* 组件复用解藕

  1. mixin：让不同的组件共用一些逻辑
     缺点：mixin引入了隐式依赖关系，耦合性强
  
  2. render props 
  
  3. hoc
  
  3. hooks

  4. 把组件拆分为容器组件和UI组件，将无状态的组件



## 性能优化

* 如何减少重绘和重排
  
  1. 尽量不要用js操作dom树，比如可以采用虚拟dom，先在内存中更改，然后一次性更改完后append上去，只需要一次重排重绘
  2. 外部引用css和内联样式块，放在header里，确保样式先被下载和解析，否则会导致页面大量重排
  3. 减少不必要的DOM层级，因为当你改变某个层级中的元素时，会导致它从根元素到最内层子元素都会改变
  4. 不要通过改变父元素的样式来改变元素的样式
  5. 有动画效果的元素尽量让它脱离文本流，这样修改元素的css可以避免重排
  6. img标签设置宽高比面重排
  7. 使用transform来做形变和位移不会导致重排


* 加快HTTP请求

  1. 使用webpack等打包工具压缩优化外部资源
  2. 优化图片：采用sprites设计风格，把多张背景图片合成一个文件，对于不同的图片采用正确的格式进行压缩，只需要发送一次请求
  3. 内联图片，采用base64对图片进行编码，将较小的图片变成dataurl嵌入到样式表中，可以缓存到本地减少请求的大小和次数
  4. 将图片裁剪至合适的大小，而不是直接在页面上进行大小的压缩 
  5. 避免空的src或者href，虽然不会影响加载时间，但任然会给服务器发送请求，增加服务器压力
  6. 因为dns请求会占据请求中很大一部分的事件，因此对于页面上的其他外链资源可以给其设置dns-prefetch进行dns预解析
  7. ajax异步请求，尽量使用get而不是post，因为post会发送两次请求，并且get可以利用缓存
  8. 对于cookie的优化：减小cookie的大小或者在静态资源上禁用cookie，设置合理的cookie的过期时间
  9. 利用浏览器的缓存机制，给资源设置cache-control和expires，或者是通过etag来标识资源，比较是否被修改过


* 加快渲染
  
  1. css放在header中，js放在body最下方
  2. 对于外部引用的script标签，使用async或者defer来异步加载js文件
  3. 路由组件懒加载
  4. 异步请求


* 代码

  1. 可以通过jsperf等工具来跑benchmark，对比不同js代码片段的工作性能，进而优化js代码


* 防抖
  
  当持续触发事件时，只有当一定时间内没有再继续出发事件，才会出发事件函数的执行

  ```javascript
  var inpur = document.getElementById("input")

    function debounce(delay) {
        let timer

        return function(value) {
            clearTimeout(timer)
            timer = setTimeout(() => {
                console.log(value)
            }, delay)
        }
    }
    
    // 变量污染？ 
    var debounceFunc = debounce(1000)
    input.addEventListener("keyup", function(e) {
        debounceFunc(e.target.value)
        // 为何这里this不丢失，下面会丢失
        // debounceFunc(this.value)
    })
  ```
  
  ```javascript
  var inpur = document.getElementById("input")
        
  function debounce(callback) {
      let timer = null

      return function() {
          clearTimeout(timer)
          timer = setTimeout(() => {
          // ？如何绑上的this
              callback.apply(this)
          }, 2000)
      }
  }

  // ？无法传入e
  // ？如何闭包，保存timer
  input.addEventListener("keyup", debounce(function(){
      console.log(this.value)
  }))
  ```
  
* 节流函数

  当持续触发事件时，保证一段时间内，只触发一次事件
  
  ```javascript
   var btn = document.getElementById("button")

    function throttle(callback, wait) {
        let timer = null;

        return function() {
            if (!timer) {
                timer = setTimeout(() => {
                    callback()
                    // ？这里为什么不用clearTimeout
                    timer = null
                }, wait)
            }
        }
    }

    btn.onclick = throttle(() => console.log(1), 2000)
  ```
  
* 图片懒加载

  <img src="" data-src="xxx.img"/>


## 项目

* Arkie
  
  是一款企业级的作图系统，根据给定的素材和规范，利用图像算法智能生成大规模的广告图，解放设计师。这样的一个项目比较突出的特征是，作为一个有不同客户定制需求的工具，我们期望一套代码能够应用不同场景，因此代码及组件的复用及管理是非常重要的。第二点是由于我们的产品可以在线即时生成大规模的广告图片，因此前端渲染性能方面也是非常核心的。

* 项目难点

  1. 组件复用
  2. 图片数据量非常大，图片懒加载/优化

* 如何组件复用

  1. 使用Monorepo来存储管理组件库
  2. 高阶组件

* 组件化

  1. 防止开发过程中合并代码时的冲突


* 登陆验证

  传统session方式：
  1. 用户发送带有用户名和密码的post请求到服务器端
  2. 服务器端验证用户信息，通过后将用户对象放在session中，session放入本地内存
  3. 服务器生成一个带有sessionId的cookie，返回给浏览器
  4. 浏览器再次发送请求时，会带上这个cookie
  5. 服务器端收到请求后会检查sessionId，正确且没有过期则可以返回相应的用户信息给浏览器

  问题：
  1. Sessions: 每次用户认证通过以后，服务器需要创建一条记录保存用户信息，通常是在内存中，随着认证通过的用户越来越多，服务器的在这里的开销就会越来越大。
  2. Scalability: 由于Session是在内存中的，这就带来一些扩展性的问题。
  3. CORS: 当我们想要扩展我们的应用，让我们的数据被多个移动设备使用时，我们必须考虑跨资源共享问题。当使用AJAX调用从另一个域名下获取资源时，我们可能会遇到禁止请求的问题。
  4. CSRF: 用户很容易受到CSRF攻击。
  
  
  token方式：
  1. 用户发送带有用户名和密码的post请求到服务器端
  2. 服务器端验证通过后，生成token并将token作为key，用户信息作为value映射到redis缓存中
  3. 服务器返回给浏览器一个带有token的cookie
  4. 浏览器再次发送请求时，会带上这个token，服务器端接受到token后去redis中查询是否存在
  
  token方案保证了服务的无状态，所有的信息都是存在分布式缓存中，支持高并发
  
  JWT（JSON Web Token）方案：
  JWT的结构：header.payload.signature(由header中的加密算法，并使用密钥对它们签名获得)
  
  1. 用户发送带有用户名和密码的post请求到服务器端
  2. 服务器端验证通过后，将用户信息进行加密，生成JSON Web Token并返回
  3. 浏览器再次发送请求时，都会带上这个JWT，通常会在请求时被拦截，放在Authorization header中
  4. 服务器端验证签名，如果通过则返回请求结果

  JWT方式将用户状态分散到了客户端中，可以明显减轻服务端的内存压力。完全无状态，可扩展。
  由于没有Cookie被发送，还有助于防止CSRF攻击。
  如果token是在授权头（Authorization header）中发送的，那么跨源资源共享(CORS)将不会成为问题，因为它不使用cookie。
  该信息可以被验证和信任，因为它是数字签名的。



## Webpack

* loader:
  
  css-loader：css-loader：将css资源编译成commonjs的模块到js中
  style-loader：将js中引入的css文件通过创建style标签的形式打包到html文件来
  image-loader：加载并且压缩图片文件，通过配置dataUrlCondition来分大小打包图片文件，小图片采用base64的方式注入到css中，可以节省http请求
  postcss-loader：解决css兼容性问题
  ts-loader：将ts文件转为js文件
  babel-loader：把 ES6 转换成 ES5
  

* Plugin
  
  mini-css-extract-plugin: 之前的style-loader打包css文件是通过import进js文件然后加载js时生成style标签，容易发生闪屏现象，mini-css-extract-plugin可以将css文件单独打包，通过link引入html，解决该问题
  eslint-plugin：通过配置各种rule来检查js语法以及统一代码风格，每个rule的级别value有off/0(关闭规则), warn/1(开启警告级别的规则), error/2
  html-webpack-plugin：简化 HTML 文件创建，并让html文件自动引入打包资源
  terser-webpack-plugin: 支持压缩 ES6 
  
  
* loader vs plugin

  Loader 本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。 因为 Webpack 只认识 JavaScript，所以loader需要对其他类型的资源进行转译的预处理工作。 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，包含了test，use等属性。
  
  Plugin 可以扩展 Webpack 的功能，在 Webpack 运行中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

