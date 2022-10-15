# Frontend_Interview

## Javascript

* isNaN & Number.isNaN

  首先我们不能用==或者===（equality operators）to compare a value against NaN来判断一个值是不是NaN，因为NaN == NaN及NaN === NaN都返回false。

  isNaN: 含义是：is this value, when coerced to a numeric value, an IEEE-754 'Not A Number' value? 会有一个implicit conversion to number。
  你只能得到true当：isNaN(undefined), isNaN({}), isNaN(NaN), isNaN('char')，空字符串，空格字符串，空数组，null，boolean都可以得到false。

  Number.isNaN: determines whether the passed value is NaN and its type is Number, no implict conversion. 只有当值为number类型（NaN也是number类型）并且不是一个数字时，才会返回true。只要本身类型不是数字 || 是一个合法的数字，都会返回false。
  因为各种字符串都不是number type，因此都会返回false。

  0/0 === NaN
  1/0 === Infinity( typeof number)
  
* call & apply & bind
  
  func.call(obj, arg1, arg2, ...) 将obj设置为this，将参数以参数列表的形式传给func并调用
  func.apply(obj, [args]) 将obj设置为this，将参数以数组的形式传递给func并调用
  func.bind(obj, arg1, arg2, ...) 将this指向obj，并传入参数，会返回一个新的函数，可以在任何地方调用但是不丢失this
  
  
## DOM & BOM

* defer 和 aync

  浏览器遇到script标签会停止构建DOM转而开始加载运行script里的脚本文件，一方面会影响页面的加载速度，一方面会导致一些还未构建的DOM无法被脚本操作。如果将脚本文件放在最后，则需要等待整个html文件加载完成。因此我们需要在script中使用属性defer或者async。注意：两者都要有外部src文件才起作用，否则被忽略。
  
  两者都会让浏览器不要等待脚本，继续处理HTML，构建DOM，页面的内容会显示。
  
  defer会在DOMContentLoaded事件之前处理完成脚本，并且脚本是会按照顺序执行的。因此defer用于需要整个DOM的脚本，或者脚本的执行顺序很重要的情况下。
  
  async是一个完全独立的脚本，它不会等待页面其他内容或者脚本执行，其他内容也不会等待它执行，包括DOMContentLoaded。因此无法决定它的执行时间，执行顺序也是按照加载优先顺序的。因此常用于完全独立于页面的一些外部脚本。
  
  还有一种动态脚本：使用 JavaScript 动态地创建一个脚本，并将其附加（append）到文档（document）中：
  ```javascript
  let script = document.createElement('script')
  script.scr = './xx.js'
  document.body.append(scrpit)
  ```
  这种方式下默认为async，除非将scrpit.async = false设置后，会变成defer。
  
  

## ES6

* 分别暴露和统一暴露


