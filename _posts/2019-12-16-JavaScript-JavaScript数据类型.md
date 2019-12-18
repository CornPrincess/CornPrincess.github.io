---
layout: post
title:  "JavaScript数据类型"
date:   2019-12-17 23:50:12 +0800
categories: JavaScript
tags: JavaScript 
---

* content
{:toc}

本篇博客主要记录JavaScript的数据类型相关的知识。

# 类型
查阅最新的[ECMAScript 2020](https://tc39.es/ecma262/#sec-ecmascript-data-types-and-values)可知，JavaScript一共有7种基本数据类型（primitives），Undefined、Null、Boolean、Number、**BigInt（新加类型）**、String、Symbol，以及Object类型

我们从运行时的角度去看 JavaScript 的类型系统。**运行时类型是代码实际执行过程中我们用到的类型**。所有的类型数据都会属于8个类型之一。从变量、参数、返回值到表达式中间结果，任何 JavaScript 代码运行过程中产生的数据，都具有运行时类型。

可以通过`typeof`操作符进行类型的确认

```JavaScript
undefined --- 值为定义
boolean --- 值为布尔值
string --- 值为字符串
number --- 值为数值
object --- 值为对象或null
function --- 值为函数
```

调用typeof null返回object，是因为特殊值null被认为是一个空的对象

# Null和Undefined

> The value null is written with a literal: null. **null is not an identifier for a property of the global object, like undefined can be.** **Instead, null expresses a lack of identification, indicating that a variable points to no object.** In APIs, null is often retrieved in a place where an object can be expected but no object is relevant.

ECMAScript在第三版中引入`undefined`，追要为了正式区分空对象指针（null）与未经初始化的变量（undefined）

```JavaScript
// foo does not exist. It is not defined and has never been initialized:
foo; //ReferenceError: foo is not defined
//typeof foo --- undefined

// foo is known to exist now but it has no type or value:
var foo = null;
foo; //null  
//typeof foo --- object
```

对null和undefined进行`==`、`===`的测试，可以看到，实际上undefined值是派生自null值的，因此null == undefined返回true。

```JavaScript
typeof null          // "object" (not "null" for legacy reasons)
typeof undefined     // "undefined"
null === undefined   // false
null  == undefined   // true
null === null        // true
null == null         // true
!null                // true
isNaN(1 + null)      // false
isNaN(1 + undefined) // true
```

如果定义的变量准备在将来保存对象，可以将变量初始化为null，这样只要直接检查null值，就可以进行对象的操作,体现**null作为空指针的惯例**，也可以区分undefined

```JavaScript
if (car != null) {
    // code 
}
```

```JavaScript
/*You can use undefined and the strict equality and inequality operators to determine whether a variable has a value. 
*/
// first alternative
var x;
if (x === undefined) {
   // these statements execute
}
else {
   // these statements do not execute
}

// x has not been declared before
if (typeof x === 'undefined') { // evaluates to true without errors
   // these statements execute
}

if (x === undefined) { // throws a ReferenceError

}

// second alternative
if ('x' in window) {
  // these statements execute only if x is defined globally 
}

// third alternative
var x;
if (x === void 0) {
   // these statements execute
}

// y has not been declared before
if (y === void 0) {
   // throws a - Uncaught ReferenceError: y is not defined
}
```

# 参考

- [如何更好的了解ECMAScript](https://zhuanlan.zhihu.com/p/22557749)
- [Standard built-in objects - null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null)
  
- [Standard built-in objects - undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)