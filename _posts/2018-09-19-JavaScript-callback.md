---
layout: post
title:  "JavaScript-callback"
date:   2018-09-19 23:59:31 +0800
categories: JavaScript
tags: 回调 同步异步 async promise await then
---

* content
{:toc}

### 回调函数
A "callback" is any function that is called by another function which takes the first function as a parameter. 在一个函数中调用另外一个函数就是callback。

举例
```javascript
function a() {
    return 1;
}

function b(aa) {
    return 2 + aa;
}

var c = 0
c = b(a())
console.log(c)
```
输出3

### 异步
在上面的例子基础上再引入另一个概念：异步
举例：
```javascript
// 设置全局变量a初值为0
var a = 0

function bb(x) {
    console.log(x)
}

// 然后过3秒后让a为6
function timer(time) {
    // setTimeout() 方法用于在指定的毫秒数后调用函数或计算表达式。
    // setTimeout(code,millisec)
    setTimeout(function () {
        a = 6
    }, time)
}

console.log(a)
timer(3000)
bb(a)
```
以上代码逻辑很简单，先设置全局变量a初值为0，然后过3秒后让a为6，接着输出a，理论上想的这次应该输出6.
但跑完代码输出结果为：
0
0
因为JS是一种异步执行语言，尽管timer函数内让a=6了，但是JS不会死等时间结束再跳出函数，
而是马上就会执行下一步语句(即调用bb函数)，但这时候3秒钟根本就没结束，a还没有被重新赋值，
所以打印出来还是为0。
用回调函数可以解决这个问题:
```javascript
var a = 0

function bb(x) {
    console.log(x)
}

function timer(time, callback) {
    // setTimeout() 方法用于在指定的毫秒数后调用函数或计算表达式。
    // setTimeout(code,millisec)
    setTimeout(function () {
        a = 6
        console.log("hi, a=" + a)
        callback(a)
    }, time)
}

console.log(a)
timer(3000, bb)
console.log("exit")
```
这次，在timer函数中添加了一个关键字callback，意思就是说此处不是一个普通的参数，而是一个函数名.
一般而言，函数的形参是指由外往内向函数体传递变量的入口，但此处加了callback后则完全相反，
它是指函数体在完成某种使命后调用外部函数的出口！
在本例中，控制台首先打印了0和exit，然后当3秒钟到了后，首先a=6，然后通过关键字callback(a)调用了函数bb(x)，结果显示：
0
exit
hi,a=6
6
这个逻辑，符合我们的需求。

在另一篇博客中，也提到回调函数的另一种理解：
 > A callback is a function that is passed as an argument to another function and is executed after its parent function has completed.

回调是一个函数被作为一个参数传递到另一个函数里，在那个函数执行完后再执行。（ 也即：B函数被作为参数传递到A函数里，在A函数执行完后再执行B 
举例：
```javascript
function A(callback){
    console.log("I am A");
    callback();  //调用该函数
}
function B(){
   console.log("I am B");
}
A(B);
```
结果："I am A", "I am B"

### 嵌套回调
```javascript
var i = 0;
function sleep(ms, callback) {
    console.log("hello")
    setTimeout(function() {
        console.log("我执行完了")
        i++
        if(i>=2) {
            callback(new Error('i大于2'), null)
        } else {
            callback(null, i)
        }
    }, ms)
}

sleep(3000, function (err, val) {
    if(err) return err.message
    console.log(val)   
    sleep(3000, function (err, val) {
        if(err) return err.message
        console.log(val)   
        sleep(3000, function (err, val) {
            if(err) return err.message
            console.log(val)   
        })
    })
})

console.log("主程序没有被阻塞")
```
输出结果：
1. 首先输出'hellp', '主程序没有被阻塞', 
2. 过三秒后输出'我执行完了', '1', 'hello',
3. 又过三秒输出'我执行完了',整个程序结束

可以看得出来，嵌套得很深，你可以把这三次操作看成三个异步任务，并且还有可能继续嵌套下去，这样的写法显然是反人类的。

嵌套得深首先一个不美观看的很不舒服，第二个如果回调函数出错了也难以判断在哪里出错的。

### 事件监听
```javascript
var i = 0;
function sleep(ms) {
    var emitter = new require('events')();
    setTimeout(function () {
        console.log('我执行完啦！');
        i++;
        if (i >= 2) emitter.emit('error', new Error('i大于2'));
        else emitter.emit('done', i);
    }, ms);
}

var emit = sleep(3000)
emit.on('done', function(val) {
    console.log("success: " + val)
})
emit.on('error', function(err) {
    console.log("fail: " + err.message)
})
```
这样写比之前的好处在于能添加多个回调函数，每个回调函数都能获得值并进行相应操作。但这并没有解决回调嵌套的问题，

比如这个函数多次调用还是必须写在ondone的回调函数里，看起来还是很不方便。

### Promise
promise和事件类似，你可以把它看成只触发两个事件的event对象，但是事件具有即时性，触发之后这个状态就不存在了，这个事件已经触发过了，你就再也拿不到值了，而promise不同，promise只有两个状态resolve和reject.
当它触发任何一个状态后它会将当前的值缓存起来，并在有回调函数添加进来的时候尝试调用回调函数，如果这个时候还没有触发resolve或者reject，那么回调函数会被缓存，等待调用，如果已经有了状态(resolve或者reject)，则立刻调用回调函数。并且所有回调函数在执行后都立即被销毁。

```javascript
var i = 0;
// 返回promise
function sleep(ms) {
    console.log("hello")
    return new Promise(function (resolve, reject) {
        setTimeout(() => {
            console.log("我执行好了")
            i++
            if( i>=2 ) reject(new Error('i>=2'))
            else resolve(i)
        }, ms);
    })
}

sleep(3000).then (function (val) {
    console.log(val)
    return sleep(3000)
}).then(function (val) {
    console.log(val)
    return sleep(3000)
}).then(function (val) {
    console.log(val)
    return sleep(3000)
}).catch(function (err) {
    console.log("出错了： " + err.message)
})

console.log("主程序没有被阻塞")
```
执行结果：
```
// 首先输出
主程序没有被阻塞
hello
// 过三秒后输出
我执行好了
1
hello
// 过三秒后输出
我执行好了
出错了： i>=2
```
这个例子中，首先它将原本嵌套的回调函数展开了，现在看的更舒服了，并且由于promise的冒泡性质，当promise链中的任意一个函数出错都会直接抛出到链的最底部，所以我们统一用了一个catch去捕获，每次promise的回调返回一个promise，这个promise把下一个then当作自己的回调函数，并在resolve之后执行，或在reject后被catch出来。这种链式的写法让函数的流程比较清楚了，抛弃了嵌套，终于能平整的写代码了。

关于promise其他[博客][1]更深入的介绍。

### async await
但promise只是解决了回调嵌套的问题，并没有解决回调本身，我们看到的代码依然是用回调阻止的。于是这里就引入了async/await关键字。
async/await是es7的新标准，并且在node7.0中已经得到支持，只是需要使用harmony模式去运行。

async函数定义:
```javascript
async function fn(){
    return 0;
}
```

async函数的特征在于调用**return返回的并不是一个普通的值，而是一个Promise对象，**如果正常return了，则返回Promise.resolve(返回值)，如果throw一个异常了，则返回Promise.reject(异常)。
也就是说async函数的返回值一定是一个promise，只是你写出来是一个普通的值，这仅仅是一个语法糖。
**await关键字只能在async函数中才能使用**，也就是说你不能在任意地方使用await。await关键字后跟一个promise对象，函数执行到await后会退出该函数，直到事件轮询检查到Promise有了状态resolve或reject 才重新执行这个函数后面的内容。

将以上程序用awiait进行改写
```javascript
var i = 0;
//函数返回promise
function sleep(ms) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            console.log('我执行好了');
            i++;
            if (i >= 2) reject(new Error('i>=2'));
            else resolve(i);
        }, ms);
    })
}

(async function() {
    try {
        var val;
        val = await sleep(3000);
        console.log(val)
        val = await sleep(3000);
        console.log(val)
        val = await sleep(3000);
        console.log(val)
    }
    catch (err) {
        console.log('出错啦: ' + err.message)
    }
} ())

console.log("主程序没有被阻塞")
```
结果：
```
// 首先输出
主程序没有被阻塞
hello
// 过三秒后输出
我执行好了
1
hello
// 过三秒后输出
我执行好了
出错了： i>=2
```
总的来说async/await是promise的语法糖，但它能将原本异步的代码写成同步的形式，try...catch也是比较友好的捕获异常的方式

参考资料：
[node.js异步控制流程 回调，事件，promise和async/await][2]
[彻底理解NodeJs中的回调(Callback)函数][3]
[javascript的回调函数 同步 异步][4]


  [1]: https://segmentfault.com/a/1190000010420744
  [2]: https://www.cnblogs.com/kazetotori/p/6043983.html
  [3]: https://blog.csdn.net/rockage/article/details/79513450
  [4]: https://www.cnblogs.com/darr/p/5207593.html