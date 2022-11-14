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
  
  以上两种
  
  3. Array.isArray()
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
  3. 路由懒加载
  4. 异步请求


* 代码

  1. 可以通过jsperf等工具来跑benchmark，对比不同js代码片段的工作性能，进而优化js代码



## 项目

* 项目难点

  1. TDD
  2. 图片数据量非常大，图片懒加载/优化

* 组件化

  1. 防止开发过程中合并代码时的冲突
  2. 






