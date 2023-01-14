# Frontend-Interview

## Javascript

* isNaN & Number.isNaN

  首先我们不能用==或者===（equality operators）to compare a value against NaN来判断一个值是不是NaN，因为NaN == NaN及NaN === NaN都返回false。

  isNaN: 含义是：is this value, when coerced to a numeric value, an IEEE-754 'Not A Number' value? 会有一个implicit conversion to number。
  你只能得到true当：isNaN(undefined), isNaN({}), isNaN(NaN), isNaN('char')，空字符串，空格字符串，空数组，null，boolean都可以得到false。

  Number.isNaN: determines whether the passed value is NaN and its type is Number, no implict conversion. 只有当值为number类型（NaN也是number类型）并且不是一个数字时，才会返回true。只要本身类型不是数字 || 是一个合法的数字，都会返回false。
  因为各种字符串都不是number type，因此都会返回false。

  Number.isNaN(0/0)
  1/0 === Infinity(typeof number)
  
  
  
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
  
* prototype对象和原型链

  每个实例对象身上都有一个隐藏的[[prototype]]属性，可以指向另一个对象或者null，这个对象就是原型对象。
  当一个对象的原型属性指向另一个新对象时，他会继承新对象的所有属性和方法，而这个新对象的原型属性再指向另一个对象，最后指向Object.prototype，即所有对象原型属性的终点。当我们调用第一个对象的属性和方法时，如果该对象身上没有这个属性或方法，则会顺着原型属性向上一层找，一直找到最后一层，然后调用它。形成了一个链式的关系。  
  
* prototype vs __prototype__ vs constructor

  prototype：函数拥有的属性，如果是构造函数，则是一个包含constructor属性指向该构造函数的对象；
  __proto__：对象拥有的属性（函数也属于对象），在实例化对象时创建，指向新建该对象的构造函数的prototype属性值；
  constructor：对象拥有的属性，指向创建该对象的构造函数
  

* JS的继承

  对象的继承包含哪几种方式：
  
  - 原型链继承：将子类型A的构造函数的隐式原型属性指向父类型B构造函数的prototype对象。缺点是，原型对象是引用共享的，会被所有实例对象共享，因此当包含引用类型的数据时，修改会造成混乱；在创建自类型的实例时不能向超类型传递参数。
  - 借用构造函数方式：通过在子类型的构造函数中嗲用超类型的构造函数来实现。可以向超类型传递参数，但是它无法实现函数的复用，造成了空间的浪费；并且超类型原型里定义的方法子类型无法使用。
  - 组合继承：通过借用构造函数的方式来实现类型的属性的继承，通过将子类型的原型设置为 超类型的实例 来实现方法的继承:
    Child.prototype = new Parent();
    // 由于重写prototype而丢失constructor属性
    Child.prototype.constructor = Child;
    缺点是：调用了两次超类的构造函数，造成了子类型的原型中多了很多不必要的属性。
  - 原型式继承：基于已有的对象来创建新的对象，向一个函数中传入一个对象，然后返回一个以该对象为原型的对象，如Object.create()
  - 寄生式继承：创建一个用于封装继承过程的函数，通过传入一个对象，然后复制一个对象的副本，然后对象进行扩展，最后返回这个对象。
  - 寄生式组合继承：借用构造函数来继承属性，通过原型链的混成形式来继承方法：
    function Child(name, age) {
      Parent.call(this, name);
      this.age = age;
    }
    // Child的prototype指向一个以Parent.prototype为原型对象的新空对象
    Child.prototype = Object.create(Parent.prototype);
    Child.prototype.constructor = Child;
  
  
* call & apply & bind
  
  func.call(obj, arg1, arg2, ...) 将obj设置为this，将参数以参数列表的形式传给func并调用
  
  func.apply(obj, [args]) 将obj设置为this，将参数以数组的形式传递给func并调用
  
  func.bind(obj, arg1, arg2, ...) 将this指向obj，并传入参数，会返回一个新的函数，可以在任何地方调用但是不丢失this
  
  call/apply实际场景：
  原型链和构造函数的继承
  把伪数组改写成数组：[...arguments] / Array.prototype.slice.call(arguments)
  js的数据类型：Object.prototyoe.toString.call(obj)
  改变this的指向
  
  手写call：
  ```javascript
  Function.prototype.mycall = functon(context) {
    const args = [...arguments].slice(1);
    if (typeof this !== "function") {
      throw new Error("Type Error");
    }
    context = context || window;
    context.fn = this;
    const result = context.fn(...args)
    delete context.fn;
    // 因为要调用所以返回调用结果
    return result;
  }
  
  ```
  
  手写bind：
  ```javascript
    Function.prototype.myBind = function(context) {
      if (typeof this !== "function") {
        throw new Error("Type Error")
      }
      let args = [...arguments].slice(1);
      context = context || window;
      context.fn = this;
      
      return function Fn() {
        args = [...args, ...arguments];
        const result = context.fn(...args);
        delete context.fn;
        return result;
      }
    }
  ```
  
  
* 判断是数组还是对象

  0. typeof不行！！
  1. a instanceof Array
  2. a.constructor === Array
  
  以上两种方法的弊端：
  用以上两种方式判断是否是array的时候，会忽略iframe元素
  
  3. Array.isArray() // 兼容性问题
  4. Object.prototype.toString.call(a) === "[object Array]" （推荐）
  
 
* typeof vs instanceof
  
  typeof用于判断primitive类型，可以判断function类型，但是null、对象和数组都为object类型。
  instanceof用于判断对象的类型，不能正确判断基本类型，其原理是判断在其原型链中是否有该类型的原型。
 
 
 * || vs &&
 
  均会对第一个操作数执行条件判断，先转为布尔值：
  ||: 如果条件判断为true返回第一个操作数值，false返回第二个操作数值；即返回遇到的第一个false
  &&: 如果条件判断为true则返回第二个操作数，false返回第二个操作数值；即返回遇到的第一个true
  
* new操作符发生了什么
  - 新建一个plain object
  - 这个新对象的原型链指向该类的构造函数的prototype对象
  - 构造函数内部的this指向新对象
  - 执行构造函数内部代码
  - 如果构造函数返回非空对象，则返回该对象，否则返回这个新建的对象
 
* 内存泄漏

  - 意外的全局变量
  - 没有取消的定时器
  - DOM元素的引用，删除该元素后DOM元素无法被回收
  - 闭包
  

* utf-8是Unicode（万国码）的一种编码方式，完全兼容ASCII的128个字符

* encodeURI vs encodeURIComponent

  encodeURI转义整个URI，因此有些URI中特殊的符号不会转义
  
* fetch

  - 基于Promise，是原生js而没有封装
  - 只会对网络请求报错，400、500都是成功的请求
  - 默认不带cookie，需要配置项 {credentials: 'include'}
  - fetch不支持abort请求或者请求超时控制
  - fetch不支持原生检测请求的进度，xhr可以


* 判断某个对象是否属于某个类

  - 用instanceof方法来判断构造函数的prototype属性是否出现在对象原型链中
  - 用对象的constructor属性来判断，看是否指向类的构造函数，但是constructor有被改写的风险
  - 判断是否是某个内置的引用类型，可以使用Object.prototype.toString.call()来打印对象的类型名




## DOM & BOM

* defer 和 aync

  浏览器遇到script标签会停止构建DOM转而开始加载+运行script里的脚本文件，一方面会影响页面的加载速度，一方面会导致一些还未构建的DOM无法被脚本操作。如果将脚本文件放在最后，则需要等待整个html文件加载完成才开始加载+执行。因此我们需要在script中使用属性defer或者async。注意：两者都要有外部src文件才起作用，否则被忽略。
  
  两者都会让浏览器不要等待脚本，继续处理HTML，构建DOM，页面的内容会显示。而脚本会进行异步加载。
  
  defer脚本异步加载完后会等待页面解析完成，DOMContentLoaded事件之前执行脚本，并且脚本是会按照顺序执行的。因此defer用于需要整个DOM的脚本，或者脚本的执行顺序很重要的情况下。
  
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



## HTML

* H5新特性

  - 语义化标签：header、footer、nav、article、main、aside、section；
  - video和audio标签，以及为了浏览器兼容性用于加载资源的source标签；
  - canvas画布，可以用于在网页上创建画布，用js绘图；
  - 增强型表单：input新类型属性，可以更好地控制输入和验证，如date、time、email、range；新表单元素progress、meter等；新表单属性：pattern、required、min/max、mutiple、autofocus；
  - navigator里新增Geolocation对象可以通过getCurrentPostion()方法来获取用户位置；
  - draggable拖放特性
  - web worker：通过Worker(x.js)构造函数来创建新的后台线程，该脚本文件会运行在该线程中，通过postMessage和onMessage来交换数据
  - 新增本地存储方式：localStorage、sessionStorage
  - WebSocket实现了服务器端和客户端的全双工通信

* meta标签

  用来描述网页文档的属性，一般由name和content来定义一组meta标签的值。
  - charset: 文档的编码方式；
  - keywords: 网页的关键字；
  - description: 网页的描述；
  - viewport: 适配移动端，可以控制视口的大小和比例。
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
    
* svg vs canvas
  - svg是一种基于xml的矢量图，放大不会导致失真，而canvas是基于像素的位图，会失真；
  - svg本质上也是页面上的元素，支持事件处理，而canvas不支持事件处理器；
  - svg中的文字和图像是独立的，文字可保留可搜索，canvas的文本渲染能力弱；
  - canvas绘制的图形可以以jpg、png方式保存，svg只能以svg的格式保存；
  - canvas渲染性能高，适合图像密集的游戏开发，而svg由于不会失真更适合做地图。
  


## CSS

* CSS3新特性

  - 新增圆角：border-radius，不需要有border也可以设置；
  - 新增元素阴影：box-shadow: x-shadow y-shadow blur-radius spread-radius color inset; 前两个是必须；
  - 文字特效：
    text-shadow，与box-shadow相似，阴影是文字本身；
    word-wrap: normal | break-word; 文字如何换行，break-word表示可以单词内换行；
    text-overflow: clip ｜ ellipsis; 文字如何处理超过容器边界的显示；
  - 新增颜色表示方式：rgba和hsla，a为透明度
  - 新增一些背景属性：
    backgroud-clip: border-box | padding-box | content-box; 确定背景的画区，默认为border-box
    background-origin: border-box | padding-box | content-box; 背景图片相对于什么来定位，默认为padding-box （定位是通过background-position: 10px 10px;）
  - 新增transition属性，过渡效果：（可以用transition简写）
    transition-property: width; 
    transition-duration: 1s;
    transition-timing-function: linear;
    transition-delay: 2s;
    
  - 新增transform属性，允许旋转、缩放、平移元素：
    transform: translate(120px, 50%)：位移
    transform: scale(2, 0.5)：缩放
    transform: rotate(0.5turn)：旋转
    transform: skew(30deg, 20deg)：倾斜
    
  - 新增animation动画属性、做预设动画：
    animation-name：动画名称
    animation-duration：动画持续时间
    animation-timing-function：动画时间函数
    animation-delay：动画延迟时间
  
  - 新增颜色渐变linear-gradient(direction, color-stop1, color-stop2,...)，radial-gradient径向渐变；
  - 新增flex布局和grid布局；

* 选择器

  100:
  - id选择器：#id；
  10:
  - class选择器：.classname；
  - 伪类选择器：:last-child  :first-of-tyle  :hover  :active；
  - 属性选择器：a[ref="eee"]；
  1:
  - 标签选择器：div；
  - 伪元素选择器：::after  ::first-letter；
  0:
  - 子代选择器：ul>li；
  - 后代选择器：li a；
  - 相邻兄弟选择器：h1+p；
  - 通配符选择器：*；


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
  1. 清除外边距重叠
  2. 水平布局
  3. 浮动元素父元素高度塌陷
  
  
* 外边距重叠

  两个块级元素的上外边距和下外边距可能会合并。
  
  兄弟元素：底部的元素设置BFC（display: inline-block、float: left、postion: absolute/fixed）
  父子元素：子元素设置BFC，父元素设置border: 1px solid transparent，父元素设置overflow: hidden/auto
  



* 清除浮动

  - 给父元素设置高度
  - 父元素设置overflow: hidden/auto
  - 使用伪元素
    ::after{
      content: "";
      display: table; 
      clear: both;
    }
  - 在最后一个浮动元素后面加一个空的元素，并设置clear:both
 



* CSS预处理器的优点：

  - 通过代码嵌套来反映css属性之间的层级关系，便于阅读和书写
  - 支持定义css变量：$name
  - 允许继承样式@extend
  - 允许使用@mixin和@include对样式进行复用
  - 可以计算样式，支持循环语句等
  - 可以将css文件模块化，实现复用


* 水平垂直居中

  - 绝对定位 + tansform：
    .parent {
        position: relative;
    }

    .child {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%,-50%);
    }
  - flex布局
    .parent {
      display: flex;
      justify-content:center;
      align-items:center;
  }

* 垂直居中
  - line-height: height


* 水平居中
  - margin: 0 auto;
    height: 100px;


* CSS为什么放在页面头部，JS为什么放在页面底部

  CSS不会阻塞DOM的构建，但是会阻塞渲染树的构建，因为渲染树需要CSS树的共同构建，从而阻塞布局影响关键渲染流程。如果将CSS放在底部，已经被解析完的内容会被渲染出来，CSS加载完成后会造成页面重新渲染，性能和体验都很不好。
  JS文件在script标签不设置async和defer的情况下，会阻塞DOM的构建，如果放在头部则会在没有执行完js文件之前一直没有html内容，体验不好。
  
  - css加载不会阻塞DOM树的解析
  - css加载会阻塞DOM树的渲染
  - css加载会阻塞后面js语句的执行


* canvas vs svg

  canvas 输出的是一整幅画布，就像一张图片，放大会失真或锯齿；svg绘制出来的每一个图形的元素都是独立的DOM节点，是矢量图形，放大缩小不会失真或锯齿。
  
  


## HTTP

* HTTP协议

  是超文本传输协议，定义了客户端与服务器之间交换数据的方式，它的通信只能由客户端发起，是一种单向请求。使用TCP作为传输层协议，保证了数据传输的可靠性。

  - 简单快速：客户发起请求时，只需要传送请求方法和路径，因此http服务器的程序规模小且通信速度快；
  - 无连接：每次连接只处理一个请求，完成后即刻断开连接，节省传输时间；
  - 无状态：没有通信过程的上下文信息，如果需要前面的信息，则必须重传，导致每次连接传送的数据量增大，但不需要先前信息时，应答会更快；
  - 灵活：允许传输任意类型的数据，由content-type标记；
  - 明文传输：协议中的报文使用的是文本形式，直接暴露给外界，不安全。
  - 无验证：不验证通信双方的身份，因此身份可能会被冒充，无法验证报文的完整性，因此可能会被篡改。


* POST和GET的区别

  - 幂等性：get是一个幂等请求，对服务器资源不会产生影响；post是一个非幂等的请求，一般会对服务器资源产生影响；
  - 缓存：一般浏览器会对get请求进行缓存，而不会对post请求进行缓存；
  - 请求报文：get的请求报文中的请求体部分为空，而post的请求报文的请求体一般为向服务器发送的数据；
  - 安全性：都不安全，但是get请求中的参数可以放在url中发送给服务器，会被浏览器保存在历史记录中；
  - 请求参数：浏览器由于对 url 长度的限制，所以会影响 get 请求发送数据时的长度。


* TCP vs UDP
  - UDP是面向无连接的，不需要在发送数据前进行握手连接，想发送就可以开始发送；TCP是面向连接的，发送数据前必须三次握手先建立可靠的连接；
  - UDP是面向报文的，UDP对应用层交下的报文不合并不拆分，保留报文的边界；TCP是面向字节流的，不保留报文边界以字节流方式进行传输；
  - UDP不仅可以一对一传输，它支持单播、多播、广播；TCP传输连接只能有两个端点，进行点对点传输，不支持多播和广播；
  - UDP是不可靠的传输，首先不需要建立连接，其次收到报文后不需要进行确认，不保证数据交付的正确性，不保证交付顺序，丢包不重传；TCP是可靠传输，可以通过编号和确认号来判断是否丢包、误码，可以保证接受方按序接受，也可以对丢失的数据进行重传；
  - UDP没有拥塞控制，会以恒定速度发送数据，可能会在网络条件不好的情况下导致丢包，对于实时性较高的场景适用；TCP拥有拥塞控制，当网络出现拥塞时，TCP能够减小向网络注入数据的速率，缓解拥塞；
  - UDP的头部开销很小，只有8字节，传输数据时很高效；TCP传送数据时头部开销较大，至少20字节。

* websocket
  是H5提供的一种浏览器与服务器进行全双工通讯的网络技术，属于应用层协议，基于TCP协议传输，浏览器和服务器只需要完成一次握手就可以直接创建持久性的连接，并进行双向数据传输。
  - 客户端和服务器端都可以向对方主动推送消息，实现实时性通信；
  - 没有同源限制，客户端可以与任意服务器通信；
  - 建立在TCP协议之上，服务器端的实现比较简单；
  - 可以发送文本也可以发送二进制数据；
  - 数据格式轻量，性能开销小；
  - 协议标识是ws，加密则是wss，与http协议兼容性良好，默认端口也为80和443，握手阶段采用http协议；
  
  使用场景：
  - 社交订阅，实况更新，多媒体聊天，弹幕。
  

* url输入后发生了什么

  - 解析url判断是否有缓存：分析url需要使用的传输协议和请求资源的路径，通过url判断请求资源是否有缓存，如果有且没有失效则直接使用缓存。
  - 域名解析获取ip地址：先判断本地是否缓存该域名的ip地址，如果有则直接使用，否则发送一个DNS请求到本地DNS服务器，本地DNS服务器也会先检查缓存是否有该域名的ip地址，没有的话则会继续向DNS根服务器进行查询，根服务器会将对应的域服务器地址返回给本地DNS服务器，层层查询后本地DNS服务器或得到该域名的IP地址，将该对应地址返回给用户电脑同时缓存在该DNS服务器中。
  - 建立连接：获得目的ip地址后，通过三次握手与服务器端建立TCP连接。
  - 获取响应：连接成功后开始客户端开始向服务器端发送http请求，服务器端会返回给客户一个html文件作为响应内容，浏览器接受到后开始对文件进行解析。
  - 页面渲染：浏览器从上往下解析html文件，首先根据html文件构建DOM树，根据css文件构建cssom树，如果遇到外部资源则会异步加载，不会阻塞渲染；遇到script标签则会先判断是否有sync或defer属性，否则会阻塞渲染。当dom树和css树构建好后，根据它们构建渲染树，渲染树完成后，浏览器先根据渲染树进行布局，然后再进行绘制，最后页面会显示出来。
  - 断开连接：若客户端认为请求已经完成，则会通过四次挥手断开连接。


* 三次握手

  - 用户向服务器发送一个SYN位为1，一个随机数作为序列号的数据包，
  - 服务器收到数据包后，向用户发送一个SYN和ACK位都为1，确认号为收到的数据包的序列号+1，一个新的随机数作为序列号的数据包，
  - 用户收到数据包后，向服务器端发送一个ACK位为1，确认号为收到数据包的序列号+1的数据包，客户端和服务器端连接建立完成，进入ESTABLISHED状态。


* 为什么要三次握手？

  防止已失效的连接请求报文在连接释放后到达服务器端，从而产生错误的连接：
  当客户端发送的连接请求报文在连接释放后到达服务器端，服务器认为要建立新的连接，如果没有第三次客户端的确认报文，那么服务器端会建立连接并等待客户端发送来的数据，白白浪费资源；但是如果有第三次客户端的确认连接，则客户端由于没有连接要建立，服务器端不会收到确认建立连接的报文，不会建立连接。
  
* 为什么要四次挥手？

  由于tcp连接是全双工的，当客户端发送关闭连接的请求时，只表示客户端单方面不再发送数据到服务器端，但还可以接受数据，并且服务器端可能还有数据要发送给客户端，因此服务器端需要将确认报文和关闭连接的报文分开发送。


* HTTP缓存：分为强制缓存和协商缓存
    强制缓存：如果浏览器判断请求的目标资源有效命中强缓存，则无需与服务端做通讯直接从内存中读取资源
    - expires：在响应头该字段设置expires时间实现强缓存，但是expires过度依赖本地时间，因此已经被废弃
    - cache-control：在响应头设置cache-control实现强缓存，通过max-age来设置强制缓存的时长；no-cache是禁止强制缓存，no-store是禁止所有缓存策略
    
    协商缓存：
    - 基于last-modified的协商缓存：需要在服务器端读出文件修改时间，并且把时间赋给响应头的last-modified字段，并设置cache-control:no-cache;
      当客户端读取到last-modified的时候，会在下次请求头中携带一个字段：if-modified-since: 从last-modified中读取的时间；服务器每次拿到资源后会对比时间看是否要返回新资源
    - 基于etag的协商缓存：根据文件内容通过md5算法计算出唯一的哈希值，服务器会把它放在响应头的etag字段返回给用户，用户每次请求时将etag的值放入请求头的if-none-match中发送给服务器
      服务器进行对比，如果和目标资源的etag完全吻合则返回304和空的响应体，如果不吻合则返回新的文件和新的响应头中的etag给客户端
    
    **Etag高于Last-modified

    - 关闭缓存的写法：Cache-Control: no-cache, no-store, must-revalidate;
    - 使用缓存的写法：Cache-Control: max-age=100000；想更新的时候更改文件url
    
    
* 浏览器缓存的位置：
  优先级从高到低分别是：
  - Service Worker：是运行在浏览器背后的独立线程，用来实现缓存功能。传输协议必须是https。分为三步：先注册Service Worker，然后监听到install事件后就可以缓存需要的文件，监听用户访问事件，下次用户在访问的时候可以通过拦截请求的方式查询是否存在缓存。缓存是持续性的。
  - Memory Cache：内存缓存，读取效率最快，但是持续性很短，tab关闭进程释放后会被释放。
  - Disk Cache：存储在硬盘上的缓存，读取速度稍慢，容量大且存储持久性高，

   
* 浏览器本地存储
 - cookie：兼容性好，在请求头中携带方便，但大小收到限制，操作不方便需要自己封装；
 - localStorage：操作方便，永久性存储，需要自己清楚，大小为5mb；
 - sessionStorage: 与localStorage类似，但是关闭页面后会被清理，并且不能在几个同源窗口之间共享；
 - IndexedDB：一个NoSQL数据库，用键值对存储数据，可以快速读取操作，当需要存储大量数据时使用。
 - Web SQL：关系型数据库，用SQL进行操作，用JS时要进行转换较为繁琐。
 
 
* cookie: 本地存储方式之一，最初是为了服务器判断请求来源是否是同一用户。
  - 一旦创建就无法修改名称；
  - 无法跨域共享；
  - 每个域名下的cookie数量不能超过20个，每个cookie的大小不能超过4kb；
  - 每次发送http请求时都会携带cookie；
  
  场景：
  - cookie和sessionid结合使用，将sessionid存入cookie中，每次请求都会携带该sessionid，服务器可以知道请求的用户是谁；
  - 统计页面的点击次数。
  
  
* localStorage：H5新引入的特性，可以存储的信息量较大。
  - 大小为5mb，可以存储更多信息；
  - 是持久存储，不会随着页面关闭而消失，需要主动清理；
  - 仅仅存储在本地，不会随着http请求被携带；
  - 受到同源策略的限制，不同源则无法访问；
  - 不能被爬虫爬取。
  
  场景：
  - 网站的主题或者皮肤，可以通过localstorage来操作；
  - 网站中的用户浏览信息可以存储在localStorage里。
  
  
* sessionStorage：H5新引入的特性，用于临时保存同一标签页的数据。
  - 关闭窗口或标签页会删除这些数据，刷新不会；
  - 本地存储，不会发送到服务器；
  - 受到同源策略限制，并且只能在同一浏览器的同一窗口下才能共享；
  - 不能被爬虫爬取。
  
  场景：
  - 存储一些网站的游客登陆信息，或者临时聊天记录。
  
  

* http vs https

  - https需要CA证书，需要付费，http不需要；
  - http是超文本传输协议，信息是明文传输的，https是基于ssl加密的传输协议，因此信息会被加密；
  - 连接方式不同，端口也不同，http的协议端口是80，https的协议是443；
  - http是简单的无状态的连接，https可以进行身份认证，更加安全。


* http版本

  http1.0：
      1. 默认非持久连接，每次请求都打开一个新的tcp连接

  http1.1：
      1. 长连接，默认开启Connection： keep-alive，一次tcp连接可以发送多个http请求
      2. 缓存处理增加了Etag，If-Match, If-None-Match，If-Unmodified-Since
      3. 新增了一些请求方法：HEAD、OPTIONS、PUT
      4. 新增host字段，指定服务器的域名
  
  http2.0：
      1. 多路复用，客户端和服务器都可以同时发送多个请求或回应，一个tcp连接中可以存在多个流的请求，每个流中都在传输带有序列标识的帧，因此可以同时发送请求并通过序列标识还原请求内容，避免了队头堵塞；
      2. 采用二进制传输，称为帧，更加的方便且健壮，而1版本采用字符串传输；
      3. 支持服务端推送，允许服务器未经请求，主动向客户端提前发送资源；
      4. 压缩了头部，一方面，头信息使用 gzip 或 compress 压缩后再发送；另一方面，在通讯双方各自缓存了一份头部field表，避免头部重复传输。


* 状态码

  - 1xx: 信息类，表示服务器已经收到请求，但处理还未完成。
  - 2xx: 请求成功，表示请求已被接受、理解和处理。
    204: 响应成功但未返回内容
    206: 成功执行了一个范围请求
  - 3xx: 重定向，表示客户端在请求成功之前还有一些处理。
    301: 永久重定向，响应报文的Location首部应该有该资源的新URL
    302: 临时重定向，响应报文的Location首部给出的URL用来临时定位资源
    304: 页面无修改，通常用于资源缓存
  - 4xx: 客户端请求错误
    401: 未授权/未验证
    403: 客户端无访问权限
    404: 没有找到请求资源，例如输入了错误的url
  - 5xx: 服务器端的错误
    500: 内部服务器错误
    503: 服务器因维护而过载或停机，暂时无法处理请求
    
* 常用响应头：

  - Allow：服务器支持的请求方法（GET、POST）
  - Content-Type：原始媒体/内容的类型，通过Content-Encoding解码后得到的类型
    包含以下四种类型：
    application/x-www-form-urlencoded：浏览器原生的form表单；
    multipart/form-data：常见的post提交方式，通常用于表单上传；
    application/json；
    text/xml；
  - Content-Encoding：列出了对当前实体消息应用的编码类型及顺序，让接收者知道需要用何种顺序解码该消息才能获得原始内容。（gzip、br）（请求头中的Accept-Encoding会告诉服务器自己可以理解的编码方式）
  - Content-Length：表示内容长度
  - Location：表示客户应当到哪里去获取文档（用于重定向）
  - Set-Cookie：设置和页面相关联的Cookie
  - Expires：该文档在什么时候缓存过期而需要重新请求
  - Last-Modified：文档最后改动的时间，通过和请求头中的If-Modified-Since进行比较从而决定是重新返回资源还是返回304给客户端，达到缓存的功能
  - Cache-Control：用在请求和响应中来实现缓存机制，多个指令以逗号分隔：Cache-Control:public, max-age=31536000
    包含类型：
    public：表明响应可以被任何对象缓存；
    private：表明响应只能被单个用户缓存，不能共享；
    no-cache：协商缓存验证，在使用缓存前强制要求把请求提交给原始服务器进行验证；
    no-store：不实用任何缓存，
    max-age：设置缓存存储的最大周期，超过则过期，单位是秒。
    

* 常用请求头：

  - Accept：请求用来告知服务器端可以处理的内容类型
  - Accept-Encoding：浏览器能够处理的压缩编码
  - Host：请求将要发送到的服务器主机名和端口号
  - Referer：当前请求页面的来源页面的地址，包含url的path部分
  - Origin：表示了请求的来源（协议、主机、端口），不包含url的path部分，可以为null
  - Cookie：由响应头中的Set-Cookie设置或者js中的Document.cookie设置，通过分号分隔，存储在客户端的HTTP Cookie中：Cookie: name=value; name2=value2; name3=value3
  - Connection：浏览器与服务器之间的连接方式：keep-alive
  

* XSS
  跨站脚本攻击，通过代码注入恶意脚本进行攻击，使之在用户的浏览器上运行从而盗取用户的信息。本质是对于用户提交给服务器的内容没有进行充分的过滤。
  1. 持久型：通过将恶意的代码片段发往服务器的数据库里并存储，当用户请求数据时浏览器会获取到恶意的代码片段并执行，恶意的代码片段的执行可能会导致用户的个人信息被第三方获得，或者发起一些恶意的请求；
  2. 反射型：简单的把用户的输入反射给浏览器，需要诱导用户点击恶意的链接或者进入恶意的网站，点击链接后恶意代码会先发送给服务器，再反射回给浏览器；
  3. DOM型：通过拦截请求将恶意代码片段插入到网页中修改页面的 DOM 节点；

  防御：
  1. 对用户提交的所有内容及url中的参数进行过滤，对于动态插入到 HTML 中的代码进行编码，使恶意脚本无法在浏览器中执行；
  2. 充分利用CSP内容安全策略（HTTP 首部中的 Content-Security-Policy）建立一个白名单，告诉浏览器哪些外部资源可以加载和执行：禁止向第三方提交数据；限制加载其他域下的资源；禁止执行内连脚本；
  3. 对cookie使用http-only：使用 http-only 标记的 Cookie 只能使用在 HTTP 请求过程中，所以无法通过 JavaScript 来读取这段 Cookie；
  4. 设置验证码来防止脚本伪装成用户执行一些操作。


* CSRF
  跨站请求伪造攻击，诱导用户点击恶意链接，利用用户的登录状态向服务器端发起跨站请求。本质是利用 cookie 会在同源请求中携带发送给服务器的特点，以此来实现用户的冒充。

  防御：
  1. Set-Cookie: widget_session=abc123; SameSite=Strict; Secure; 通过设置cookie的SameSite属性，如果是从第三方站点发起的请求，那么需要浏览器禁止发送某些关键 Cookie 数据到服务器；
  2. 进行同源检测：Referer 是 HTTP 请求头中的一个字段，记录了该HTTP请求的来源地址，服务器端可以通过请求头中的referer或origin属性来过滤掉第三方站点发出的请求；
  3. csrf token：用户打开页面的时候，服务器需要生成一个Token发给用户，用户拿到token存在本地而不是cookie中，当用户发送 HTTP 请求，以参数的形式加入 token，或者在http头中自定义token属性，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求；
  4. cookie的双重验证：服务器会在用户访问网站时注入一个随机字符串作为cookie，在用户再次向服务器发送请求时，将这个cookie中的随机数添加到url参数中，服务器通过对比url中的数据和cookie中的数据来判断是否是csrf攻击。因为攻击者无法获取cookie只能利用cookie。


* 同源策略
  同源策略限制了从同一个源加载的文档或脚本如何与另一个源的资源进行交互，是为了保证用户的信息安全，对js脚本的一种限制。同源指协议、域名、端口号必须一致。跨域请求是可以发送成功的，只是被浏览器拦截了。
  - 当前域下的 js 脚本不能够访问其他域下的 cookie、localStorage 和 indexDB；
  - 当前域下的 js 脚本不能够操作访问操作其他域下的 DOM；
  - 当前域下 ajax 无法发送跨域请求；
  - 同源策略不会对一般的img或者script脚本进行跨域的限制。
   

* CORS
  CORS跨域资源共享是一种机制，使用额外的HTTP头来告诉浏览器让运行在一个源服务器上的web应用被批准访问来自不同源服务器上的指定资源，此时资源会发起一个跨域HTTP请求。默认情况下CORS请求不携带Cookie。
  CORS分为简单请求和非简单请求：
  - 简单请求：不会触发CORS预检请求。只要请求方法是HEAD、GET、POST三种之一并且请求头不超过以下字段：Accept、Accept-Language、Content-Language、Last-Event-ID、Content-Type。会在请求头部添加Origin字段来告诉服务器这是来自哪里的请求，服务器判断Origin指定的域名是否在许可范围内，如果在的话会在响应头中添加：Access-Control-Allow-Origin: 与Origin一致。否则没收到该响应头字段则浏览器视为无法跨域。
  - 非简单请求：不满足以上条件属于非简单请求，会在跨域请求前发送一次HTTP预检请求，使用的请求方法是OPTIONS，询问当前所在网页是否在跨域允许的范围内以及可以使用哪些http请求方式，只有得到肯定的回复才会进行正式跨域请求。返回的响应头中必须携带：Access-Control-Allow-Origin、Access-Control-Allow-Methods、Access-Control-Allow-Headers。
  

* CORS请求如何携带Cookie：

  - 在请求中设置withCredentials：xhr.withCredentials = true;
  - Access-Control-Allow-Credentials 设置为 true；
  - Access-Control-Allow-Origin 设置为非 *****。
  

* 如何解决跨域问题

  - CORS
  - JSONP：利用script标签不受同源限制的特性，通过src向服务器传送带有callback参数的GET请求，服务器端将数据拼接到callback函数中传给浏览器，浏览器执行后可以获得callback返回的数据。只支持get方法，不安全可能会遭受xss攻击；
  - postMessage可以实现不同标签页或者主页面和iframe页面之间的跨域数据传递；
  - Nginx反向代理实现跨域：通过Nginx配置一个代理服务器域名与客户端相同，当通过代理服务器与服务器进行通信时，没有浏览器参与故不受同源策略的限制；
  - node中间件实现跨域代理：开启一个与客户端地址相同的代理服务器，将请求发送到代理服务器上，再通过代理服务器进行转发。



* CDN

  内容分发网络，通过互联网相连接的电脑网络系统，利用最靠近每位用户的服务器，高性能、可扩展、低成本地将内容传递给用户。组成
  - 分发服务系统：cache设备，把缓存在本地的内容快速提供给用户，同时负责与源站点进行内容同步。Cache设备的数量、规模、总服务能力是衡量一个CDN系统能力的基本指标。
  - 负载均衡系统：负责对所有发起服务请求的用户进行访问调度，确定提供给用户的最终实际访问地址。
  - 运营管理系统

* CDN提供服务的过程

  - 用户输入url后，经过DNS服务器的解析，发现对应的是一个CDN专用的DNS服务器，于是将域名解析权交给CDN专用的DNS服务器；
  - 该CDN专用DNS服务器将一个CDN的全局负载均衡设备IP地址返回给用户；
  - 用户向CDN全局负载均衡设备发起数据请求；
  - CDN全局负载均衡设备根据用户的IP地址及用户请求的内容URL，选择一台用户所在区域的CDN区域负载均衡设备；
  - CDN区域负载均衡设备选择一台合适的缓存服务器，将该缓存服务器的ip地址返回给全局负载均衡设备，全局负载均衡设备将ip地址返回给用户；
  - 用户向该缓存服务器发起请求，缓存服务器将缓存内容发送给用户。


* 进程与线程

  - 进程是计算机进行资源分配和调度的基本单位，是程序执行的基本实体；线程是CPU调度的最小单位，一个进程中可以并发多个线程；
  - 进程之间无法共享数据互不干扰；线程之间互相分享数据；
  - 进程中的任意一个线程出错，会导致整个进程崩溃；进程之间不会互相影响；
  
 * 浏览器的组成
 
   - 一个浏览器主进程：控制除标签页之外的用户界面，对子进程进行管理，提供存储功能；
   - 一个网络进程：发送接受网络请求；
   - 一个GPU进程：负责整个浏览器界面的渲染；
   - 多个插件进程：控制并运行所有网站使用的插件；
   - 多个渲染进程：控制显示tab标签内的所有内容，核心任务是将 HTML、CSS 和 JavaScript 转换为用户可以与之交互的网页，排版引擎 Blink 和 JavaScript 引擎 V8 都是运行在该进程中。（内核）

* 渲染进程中的线程：

  - GUI渲染线程：负责渲染网页，解析html、css，构建dom树、css树及render树，根据布局绘制页面；
  - JS引擎线程：负责处理JS脚本，解析并运行代码，一个tab中只能同时有一个js线程，并且js线程和gui线程的执行也是互斥的；
  - 事件触发线程：用来控制事件循环，当js引擎执行一些异步操作、计时器、触发事件时，会将对应任务添加到该线程的待处理队列的队尾，等待js引擎空闲后处理；
  - 定时器触发进程：用来对定时器进行计时并触发，计时完毕后添加到事件队列中；
  - 异步http请求线程：XMLHttpRequest连接后通过这个线程发送请求，回调函数会被放入事件队列中等待js引擎空闲后执行。


* 浏览器标签页的通信

  - 使用websocket：websocket协议可以实现服务器推送，服务器可以当作中介者，标签页向服务器发送的消息再被推送给其他标签页；
  - 使用localStorage：可以在一个标签页对localStorage的变化事件进行监听，当另一个标签页修改数据时，可以通过这个监听事件获取到数据；
  - 使用SharedWorker：shardeWorker会在多个页面间创建一个唯一的线程，作为共享线程，标签页通过共享线程来实现数据交换；
  - 使用postMessage方法：postMessage是h5新引进的一个可跨源通信的api，可以在不同窗口间或主页面与嵌套的iframe间进行通讯，postMessage(data, origin)，监听message事件来进行数据交互。


* 服务器向浏览器推送消息：
  - 短轮询：每隔一段时间就向服务器发送消息，无论是否有数据更新都进行响应，操作简单但是浪费服务器端和客户端的资源；
  - 长轮训：客户端向服务器发送请求，收到请求后服务器先将请求挂起，如果有数据更新则进行响应，如果没有直到时间限制后进行响应，客户端在处理完服务器的返回后再次发出请求，比短轮询减少了不必要的http请求；
  - websocket：H5新协议，全双工。
  - SSE（Server-Sent Events）：服务器使用流信息向客户端进行消息推送。当服务器向客户端声明将要发送流信息，客户端则不会关闭连接一直等待服务器发送数据流，是基于http协议的。比如视频播放。


* RESTful
  一种最流行的接口设计规范：
  - 面向资源的：要求接口的url中的path来指定资源时用小写英文名词的复数形式，不出现动词；
  - 用http请求方式来表示请求的动作：GET表示查询，POST表示新增，PUT表示更新，DELETE表示删除；
  - 服务器响应包含状态码来表明请求的结果是否成功。



## REACT

* React的理解

  React 是一个网页 UI 框架，通过 组件化 的方式解决 视图层 开发 复用 的问题。它的核心设计思路有三点，分别是声明式、组件化与 通用性。
  声明式：优势在于直观与组合
  组件化：优势在于视图的拆分与模块复用，实现了高复用低耦合
  通用性：在于一次学习，随处编写，比如 React Native，React 360 

* React的事件机制
  
  - 事件并不是绑定在真实DOM上，而是通过事件代理的方式，在document处监听了事件，当冒泡到document处时交由时间处理函数处理；
  - React中的事件也并非原生浏览器事件，而是由自己封装的合成事件，合成事件首先可以抹平浏览器的兼容问题，其次合成事件通过事件池来专门管理事件的创建和销毁，当事件需要被使用时可以从池中复用，节约了内存；
  - React中的事件需要用prevetDefault()方法来阻止默认行为，return false不行。


* 虚拟DOM

  通过JS对象来模拟DOM中的节点，减少对于真实DOM的操作，因为操作JS对象的性能会比操作真实DOM要高很多；当状态发生变更时，将变更前后的虚拟 DOM 树进行差异比较，这个过程称为 diff，diff算法能够大大提升渲染性能，而diff算法也是基于虚拟DOM的高效算法。生成的结果称为 patch，即需要更新的DOM。计算之后，会渲染 Patch 完成对真实 DOM 的操作。

* Fiber

  每当我们触发一次组件的更新，React 都会构建一棵新的虚拟 DOM 树，通过与上一次的虚拟 DOM 树进行 diff，这个过程是个递归的过程。同步渲染的递归调用栈是非常深的，只有最底层的调用返回了，整个渲染过程才会开始逐层返回。而一旦渲染占用了主线程，则完成之前用户无法进行任何操作。Fiber 会将一个大的更新任务拆解为许多个小任务。每当执行完一个小任务时，渲染线程都会把主线程交回去，看看有没有优先级更高的工作要处理。这就是所谓的异步渲染。
  Fiber 架构的重要特征就是可以被打断的异步渲染模式。React 16 的生命周期被划分为了 reconciliation 和 commit 两个阶段，而 commit 阶段又被细分为了 pre-commit 和 commit。reconciliation 阶段在执行过程中允许被打断，而 commit 阶段则总是同步执行的。因为reconciliation阶段用户无法感知，而commit 阶段的操作则涉及真实 DOM 的渲染。
  
* React渲染页面的两个阶段
  调度阶段（reconciliation）：在这个阶段 React 会更新数据生成新的 Virtual DOM，然后通过Diff算法，快速找出需要更新的元素，放到更新队列中去，得到新的更新队列。
      更新 state 与 props；
      调用生命周期钩子；
      生成 virtual dom，这里应该称为 Fiber Tree 更为符合；
      通过新旧 vdom 进行 diff 算法，获取 vdom change
      确定是否需要重新渲染
  渲染阶段（commit）：这个阶段 React 会遍历更新队列，将其所有的变更一次性更新到DOM上
  
* Fiber & Hook
  Fiber数据结构：vdom 树会被转成 fiber 链表，然后再渲染 fiber。Fiber数据结构中，FiberNode上有 memoizedState 和 updateQueue 属性，函数组件会将内部用到的所有的 hook 通过单向链表的形式，保存在组件对应 fiber 节点的 memoizedState 属性上。updateQueue 是 useEffect 产生的 effect 连接成的环状单向链表。
  Hook数据结构：hook 的 memoizedState 存的是当前 hook 自己的值。baseQueue记录由于之前某些高优先级任务导致更新中断，尚未处理的最后一个 update。每个 useXxx 的 hooks 都有 mountXxx 和 updateXxx 两个阶段。链表只创建一次，在 mountXxx 当中，后面都是 update。在mount阶段，调用mountWorkInProgressHook 通用方法，所有 hook 都会执行，通过它新建 hook 对象，如果前面没有hook 对象，就将该 hook 挂到当前 fiber 节点的 memoizedState上面，否则接到前一个 hook 对象的 next 上，构成单向链表。
  
* 为什么不能在循环、条件等嵌套中调用hook

  各个 hook 在 mount 时会以链表的形式挂到 fiber.memoizedState上。在update时获取hook对象需要遍历列表，通过顺序索引到当前需要的hook对象，如果打乱了顺序或者进行嵌套则会无法正确更新。
  
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


* 组件隐藏不卸载

  1. 用div包裹组件，将css设置为display:none
  2. Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的方式，先通过 document.createElement 在内存中创建一个元素，然后再通过 React.createPoral 把 React 子节点渲染到这个元素上，这样就实现了“空渲染”。

  ```javascript
    const targetElement = document.createElement('div')
    ReactDOM.createPortal(child, targetElement)
  ```
  我们可以进一步封装出一个 Conditional 组件，从而实现通用性的条件渲染逻辑：
  
  ```javascript
    export const Conditional = props => {
    const [targetElement] = useState(() => document.createElement('div'))
    const containerRef = useRef()
    useLayoutEffect(() => {
      if (props.active) {
        containerRef.current.appendChild(targetElement)
      } else {
        try {
          containerRef.current.removeChild(targetElement)
        } catch (e) {}
      }
    }, [props.active])
    return (
      <>
        <div ref={containerRef} />
        {ReactDOM.createPortal(props.children, targetElement)}
      </>
    )
  }
  ```
  
  首先，我们创建了一个 targetElement ，并且通过 createPortal 将 children 渲染到 targetElement 。然后，我们会创建一个容器 div 元素，并且通过 containerRef 拿到它的引用。最后，当 active 为 true 时，我们会把 targetElement 手动添加到 containerRef.current 的内部，反之，则会从其内部移除掉 targetElement 。实际使用的方式如下：
  
  ```javascript
    <Conditional active={!shouldHide}>
      <Foo/>
    </Conditional>
  ```
  
  
* React18 新特性

  - 为了更好的管理root节点，React 18 引入了一个新的 root API，同时，在卸载组件时，我们也需要将 unmountComponentAtNode 升级为 root.unmount：
    ```javascript
    import React from 'react';
    import ReactDOM from 'react-dom/client';
    import App from './App';
    
    const root = document.getElementById('root')!;
    ReactDOM.createRoot(root).render(<App />);
    ```
  - 批处理：批处理是指为了获得更好的性能，在数据层，将多个状态更新批量处理，合并成一次更新（在视图层，将多个渲染合并成一次渲染）
    在React 18 之前，我们只在 React 事件处理函数 中进行批处理更新。在 18 之后，任何情况都会自动执行批处理，多次更新始终合并为一次。
    
  - 在 React 18 的 Suspense 组件中，官方对 空的fallback 属性的处理方式做了改变：不再跳过 缺失值 或 值为null 的 fallback 的 Suspense 边界。相反，会捕获边界并且向外层查找，如果查找不到，将会把 fallback 呈现为 null。


* Redux vs Mobx

  - redux中只有一个store，而mobx中有多个store
  - redux使用plain object来保存状态数据，需要手动处理变化的操作，而mobx是用observable保存数据，可以自动响应数据的操作
  - redux的状态不可改，使用纯函数返回一个新的状态，mobx中的状态可以直接修改
  - mobx更多的抽象和封装，使用面向对象的思想，更难预测结果和调试，redux采用函数式编程思想，更容易调试



## 性能优化

* 如何减少重绘和重排
  
  1. 尽量减少用js操作dom树，比如可以采用虚拟dom，先在内存中更改，然后一次性更改完后append上去；或者创建文档片段documentFragment（没有父对象的最小文档对象），在上面应用DOM操作，最后再append到真实DOM上，因为documentFragment不是真实的DOM树，因此不会触发重新渲染，只需要一次重排重绘；
  2. 尽量减少用js直接操作style，相比而言可以更改类名来更改样式；
  3. 外部引用css和内联样式块，放在header里，确保样式先被下载和解析，否则会导致页面大量重排；
  4. 减少不必要的DOM层级；
  5. 不要通过改变父元素的样式来改变元素的样式，尽可能在低层级DOM上改变样式；
  6. 有动画效果的元素尽量让它脱离文本流，减少对其他元素的影响，比如用absolute或fixed定位；
  7. 或者使用transform来做形变和位移及动画效果不会导致重排，因为重绘重排在主线程上发生，而transform是直接通过合成线程画出位图，将位图发送给GPU进行绘制，不会触发重绘重排；
  8. img标签设置宽高避免重排；
  9. 不要使用table布局，因为很小的改动会造成整个table的重新布局，或者设置table-layout:auto;或者是table-layout:fixed；
  10. 因为浏览器本身会将所有重排重绘操作放入一个队列，当到达一定数量或者一定时间后浏览器对队列进行批处理，因此可以将多个读操作和多个写操作放在一起，就可以将多次重排合并为一次；


* 加快HTTP请求

  1. 使用webpack等打包工具压缩优化外部资源
  2. 优化图片：采用sprites设计风格，把多张图标合成一个图片文件，只需要发送一次请求
  3. 采用base64对图片进行编码，将较小的图片嵌入到样式表中，可以缓存到本地减少请求的大小和次数
  4. 将图片裁剪至合适的大小，而不是直接在页面上进行大小的压缩
  5. 选择正确的图片格式，能用WebP的尽量用WebP，因为图像数据压缩算法好，小图用png，图标用svg，在乎图片质量的用jpeg
  6. 避免空的src或者href，虽然不会影响加载时间，但任然会给服务器发送请求，增加服务器压力
  7. 因为dns请求会占据请求中很大一部分的事件，因此对于页面上的其他外链资源可以给其设置dns-prefetch进行dns预解析
  8. ajax异步请求，尽量使用get而不是post，因为post会发送两次请求，并且get可以利用缓存
  9. 对于cookie的优化：减小cookie的大小或者在静态资源上禁用cookie，设置合理的cookie的过期时间
  10. 利用浏览器的缓存机制，给资源设置cache-control和expires，或者是通过etag来标识资源，比较是否被修改过


* 加快渲染
  
  1. css放在header中，js放在body最下方；
  2. 对于外部引用的script标签，使用async或者defer来异步加载js文件；
  3. 路由组件懒加载；
  4. 优化css选择器，不要太复杂；
  5. 异步请求。


* 代码

  1. 可以通过jsperf等工具来跑benchmark，对比不同js代码片段的工作性能，进而优化js代码


* 防抖
  
  当持续触发事件时，只有当一定时间内没有再继续出发事件，才会出发事件函数的执行

  ```javascript
  function debounce(fn, time) {
    let timer = null;
    return function() {
      const context = this;
      const args = [...arguments];
      if(timer) {
        clearTimeout(timer);
        timer = null;
      }
      timer = setTimeout(() => {
        fn.call(context, ...args);
      }, time * 1000)
    }
  }
  ```
  
* 节流函数

  当持续触发事件时，保证一段时间内，只触发一次事件
  
  ```javascript
   function throttle(fn, time) {
      let timer = null;
      return function() {
        const context = this;
        const args = [...arguments]
        if (!timer) {
          timer = setTimeout(() => {
            fn.apply(context, args)
            timer = null;
          }, time * 1000)
        }
      }
   }
  ```
  
* 图片懒加载

```javascript
  <img src="" data-src="xxx.img"/>
```


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
  asset-module：加载并且压缩图片文件，通过配置dataUrlCondition来分大小打包图片文件，小图片采用base64的方式注入到css中，可以节省http请求
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
  
  
* 减少build时间

  1. thread-loader：多线程打包，使用：将其放在比较耗时的loader前，比如babel-loader；还可以通过HappyPack将耗时长的loader放进并行的线程里
  2. cache-loader：缓存打包资源，提高二次构建的速度，使用：将其放在比较耗时的loader前
  3. HotModuleReplacementPlugin：在开发过程中开启热更新，如果有修改文件，只刷新该模块，其他保持不变
    ```javascript
    //使用webpack提供的热更新插件
     plugins: [
        new webpack.HotModuleReplacementPlugin()
     ],
    //最后需要在我们的devserver中配置
    devServer: {
      hot: true
    },
    ```
    
  4. exclude和include：合理设置 需要处理的文件 和 不需要处理的文件 这两个属性
    ```javascrip
    //使用exclude排除指定文件夹
        exclude: /node_modules/,
    ```
    
  5. 构建区分环境：在配置时需要明确区分哪些配置用于开发环境，哪些配置用于生产环境
      开发环境：希望减少构建时间，去除代码压缩、gzip、体积分析等优化的配置，大大提高构建速度
      生产环境：希望减小最终项目的打包体积需要代码压缩、gzip、体积分析等优化的配置，大大降低最终项目打包体积
  
  6. 因为babel非常影响打包速度，转换的代码越多，效率越低。因此配置babel-loader时，只作用在src下的js文件上，并且要排除node_modules，同时可以将babel编译过的文件缓存起来，这样下次只需要编译更改过的代码文件：
  ```javascripy
    rules: [
      {
        test: /\.js$/,
        loader: 'babel-loader?cacheDirectory=true',
        include: [resolve('src')],
        exclude: /node_modules/
      }
    ]
  ```
  
  7. 使用DllPlugin来将特定的类库提前打包引入，极大减少打包类库的次数，只有当类库更新版本时才有需要重新打包，实现了公共代码抽离成单独文件的优化方案。

  

* 减少打包体积
    （代码的压缩比较耗时间，所以只用在打包项目时，所以只需要在webpack.prod.js中配置）
   - css-minimizer-webpack-plugin 压缩、去重css文件
   - terser-webpack-plugin 压缩js代码

   ```javascript
   optimization: {
      minimizer: [
          new CssMinimizerPlugin(),
          new TerserPlugin({
              terserOptions: {
                  compress: {
                      drop_console: true // 去除console
                  }
              }
          })
      ]
   }
   ```
   - tree-shaking：只打包用到的代码，没用到的不打包，webpack4在production模式下默认开启
  
* source-map
  方便在报错的时候定位到错误的文件位置
  开发环境：我们需要精准的错误定位
  ```javascript
  devServer: {
    ...
  },
  mode: development,
  devtool: 'eval-cheap-module-source-map',
  ```
  
  生产环境：希望错误定位但不想打包体积大
  ```javascript
  mode: production,
  devtool: 'nosources-source-map'
  ```
  
* 提升用户体验
  （只需要在生产环境中配置）
  1. 如果不进行模块懒加载则整个项目都会被打包进一个js文件里，体积很大，网页请求的时候，首屏加载会时间很长 

    ```javascript
    const routes = [
      {
        path: '/login',
        name: 'login',
        component: login
      },
      {
        path: '/home',
        name: 'home',
        // 懒加载
        component: () => import('../views/home/home.vue'),
      },
    ]
    ```
    
  2. Gzip：开启Gzip后，大大提高用户的页面加载速度，因为gzip的体积比原文件小很多，需要服务器 nginx 配合，使用compression-webpack-plugin
    ```javascript
    plugins: [
      new CompressionPlugin({
        algorithm: 'gzip',
        threshold: 10240,
        minRatio: 0.8
      })
    ]
    ```
    
  3. asset-module：对于小图片转为base64，减少发送http请求的次数
  ```javascript
    {
        test: /\.(png|jpe?g|gif|svg|webp)$/,
        type: 'asset',
        parser: {
          dataUrlCondition: {
            maxSize: 25 * 1024, // 25kb
          }
        },
        generator: {
          filename: 'image/[contenthash][ext][query]'
        }
    }
  ```
  
  4. 减少打包体积的如：css-minimizer-webpack-plugin、terser-webpack-plugin、tree-shaking
  5. 合理配置hash：保证改过的文件要改变hash值，而没改过的文件保持原来的hash，更好命中缓存
    ```javascript
    output: {
      path: path.resolve(__dirname, '../dist'),
      // 给js文件加上 contenthash
      filename: 'js/chunk-[contenthash].js',
      clean: true,
    },
    ```
  6. splitChunks：拆包
    ```javascript
        optimization: {
            splitChunks: {
                cacheGroups: {
                    vendor: {
                        name: 'vendor',
                        test: /[\\/]node_modules[\\/]/,
                        minSize: 0,
                        minChunks: 1,
                        priority: 10,
                        chunks: 'initial'
                    },
                    common: {
                        name: 'common',
                        test: /[\\/]src[\\/]/,
                        chunks: 'all',
                        minSize: 0,
                        minChunks: 2
                    }
                }
            }
        },
    ```
    
      
## AntD
  
1. 如何设置主题：

  ```javascript
    import { ConfigProvider, Button } from 'antd';

    const App: React.FC = () => (
      <ConfigProvider
        theme={{
          token: {
            colorPrimary: '#00b96b',
          },
        }}
      >
        <Button />
      </ConfigProvider>
    );
  ```
  
  
  
  
  
  
  
  
  
