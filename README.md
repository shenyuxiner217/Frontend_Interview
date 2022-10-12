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

## ES6

* 分别暴露和统一暴露
