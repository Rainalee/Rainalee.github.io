---
layout: default
title: Another page
description: This is just another page
---

## this 指向

随着函数使用场合的不同，this的值会发生变化。但是有一个总的原则，那就是this指的是调用函数的那个对象

那么可以从下面四个方向来分析this的用法
*   纯碎的函数调用
*   作为对象的方法调用
*   作为构造函数来调用
*   使用 apply 或 call 调用

### 情况一：纯碎的函数调用

```js
function makeNoSense(x) { 
this.x = x; 
} 
 
makeNoSense(5); 
x;// x 已经成为一个值为 5 的全局变量
```
这是函数的最通常用法，属于全局性调用，因此this就代表全局对象Global。


### 情况二：作为对象的方法调用
```js
var point = { 
x : 0, 
y : 0, 
moveTo : function(x, y) { 
    this.x = this.x + x; 
    this.y = this.y + y; 
    } 
}; 
 
point.moveTo(1, 1)//this 绑定到当前对象，即 point 对象

```
这里的this指向的是对象point，因为调用这个moveTo是通过point.moveTo()执行的，那自然指向就是对象point，这里再次强调一点，this的指向在函数创建的时候是决定不了的，在调用的时候才能决定，谁调用的就指向谁。

```js
var o = {
    user:"name",
    fn:function(){
        console.log(this.user); //name
    }
}
window.o.fn();
//-------------------
var o = {
    a:10,
    b:{
        a:12,
        fn:function(){
            console.log(this.a); //12
        }
    }
}
o.b.fn();
```
可见this在不同调用的场景下this所指也不相同
1.  情况1：如果一个函数中有this，但是它没有被上一级的对象所调用，那么this指向的就是window，这里需要说明的是在js的严格版中this指向的不是window。
2.  情况2：如果一个函数中有this，这个函数有被上一级的对象所调用，那么this指向的就是上一级的对象。
3.  情况3：如果一个函数中有this，这个函数中包含多个对象，尽管这个函数是被最外层的对象所调用，this指向的也只是它上一级的对象。

还有一种情况比较特殊
```js
// 例4
var o = {
    a:10,
    b:{
        a:12,
        fn:function(){
            console.log(this.a); //undefined
            console.log(this); //window
        }
    }
}
var j = o.b.fn;
j();
```
this永远指向的是最后调用它的对象，也就是看它执行的时候是谁调用的，例子4中虽然函数fn是被对象b所引用，但是在将fn赋值给变量j的时候并没有执行所以最终指向的是window

### 情况三：作为构造函数来调用
```js
function Fn(){
    this.user = "userName";
}
var a = new Fn();
console.log(a.user); //userName
```
之所以对象a可以找出函数Fn里面的user是因为new关键字可以改变this的指向，将这个this指向对象a

还有一种情况比较特殊
```js
function fn()  
{  
    this.user = 'userName';  
    return {};  //undefined
    return function () {}; //undefined
    return []; //undefined
    return 1; //userName
    return undefined; //userName
    return null; //userName
}
var a = new fn;  
console.log(a.user); //undefined
```
就是说：如果返回值是一个对象，那么this指向的就是那个返回的对象，如果返回值不是一个对象那么this还是指向函数的实例。但是null比较特殊，虽然也是对象，但是在这里this还是指向那个函数的实例

### 情况三：使用 apply 或 call 调用
apply、call、bind都是可以改变this指向的函数对象的方法，只不过用法不同
```js
    var x = 0
    function test(){
        alert(this.x);
    }
    var o={};
    o.x = 1;
    o.m = test;
    o.m.apply(); //0
```
apply()的参数为空时，默认调用全局对象。因此，这时的运行结果为0，证明this指的是全局对象。
```js
function Point(x, y){ 
   this.x = x; 
   this.y = y; 
   this.moveTo = function(x, y){ 
       this.x = x; 
       this.y = y; 
   } 
} 
 
var p1 = new Point(0, 0); 
var p2 = {x: 0, y: 0}; 
p1.moveTo(1, 1); 
p1.moveTo.apply(p2, [10, 10]);
```
在上面的例子中，使用构造函数生成了一个对象 p1，该对象同时具有 moveTo 方法；使用对象字面量创建了另一个对象 p2，我们看到使用 apply 可以将 p1 的方法应用到 p2 上，这时候 this 也被绑定到对象 p2 上。另一个方法 call 也具备同样功能，不同的是最后的参数不是作为一个数组统一传入，而是分开传入的。


[back](./)
