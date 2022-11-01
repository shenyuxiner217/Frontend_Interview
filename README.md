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
