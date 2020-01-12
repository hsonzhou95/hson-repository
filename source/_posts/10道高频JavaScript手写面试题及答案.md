---
title: 10道高频JavaScript手写面试题及答案
date: 2020-01-07 14:23:31
tags:
- JavaScript
comments: true
type: "JavaScript"
categories:
- JavaScript
---
## 10道高频JavaScript手写面试题及答案
<!-- more -->
### **JavaScript笔试部分**

#### 深拷贝（deepclone）

简单版：

```javascript
const newObj = JSON.parse(JSON.stringify(oldObj));
```

局限性 ：

- 1、他无法实现函数、RegExp等特殊对象的克隆

- 2、会抛弃对象的constructor，所有的构造函数会指向Object

- 3、对象有循环引用，会报错

#### **实现Event (event bus)**

> event bus既是node中各个模块的基石，又是前端组件通信的依赖手段之一，同时涉及了订阅-发布设计模式，是非常重要的基础。

```javascript
class EventEmeitter {
    constructor(){
        this._events = this._events || new Map(); //储存事件/回调键值对
        this._maxListeners = this._maxListeners || 1o;//设立监听上限
    }
}

//触发名为type的事件
EventEmeitter.prototype.emit = function(type,...args){
    let hander;
    //从储存事件键值对的this._events中获取对应事件回调函数
    handler = this._events.get(type);
    if (args.length > 0) {
        hander.apply(this,args);
    }else{
        handler.call(this);
    }
    return true;
};

//监听名为type事件
EventEmeitter.prototype.addListener = function(type,fn) {
    //将type事件以及对应的fn函数放入this._events中储存
    if (!this._events.get(type)) {
        this._events.set(type,fn);
    }
};
```

#### 实现instanceOf

```javascript
//模拟 instanceof
function instance_of(L,R){
    var O = R.prototype;//取 R 的显示原型
    L = L.__proto__;//取 L 的隐式原型
    while (true) {
        if (L === null) return false;
        if (O === L)
        // 这里重点 ：当 O 严格等于 L 时，返回 true
        return true;
        L = L.__proto__;
    }
}
```

#### 模拟new

##### new操作符做了这些事：

- 他创建了一个全新的对象
- 他会被执行[[Prototype]] (也就是__proto__) 链接
- 它使this指向新创建的对象
- 通过new创建的每个对象将最终被[[Prototype]]链接到这个函数的prototype对象上
- 如果函数没有返回对象类型Object(包含Function，Array，Date，RegExg，Error)，那么new表达式中的函数调用将返回对象引用

```javascript
// objectFactory(name,'cxk','18')
function objectFactory(){
    const obj = new object();
    const Constructor = [].shift.call(arguments);

    obj.__proto__ = Constructor.prototype;

    const ret = Constructor.apply(obj,arguments);

    return typeof ret === "object" ? ret : obj;
}
```

#### 实现一个call

##### call做了什么：

- 将函数设为对象的属性
- 执行&删除这个函数
- 指定this到函数并传人给定参数执行函数
- 如果不传人参数，默认指向为 window

```javascript
//模拟 call bar.mycall(null);
//实现一个call方法；
Function.prototype.myCall = function(context){
    //此处没有考虑context非object情况
    context.fn = this;
    let args = [];
    for (let i = 1,len = arguments.length,i < len; i++){
        args.push(arguments[i]);
    }
    context.fn(...args);
    let result = context.fn(...args);
    delete context.fn;
    return result;
};
```

#### 实现apply方法

##### apply原理与call很相似

```javascript
//模拟 apply
Function.prototype.myapply = function(context,arr){
    var context = Object(context) || window;
    context.fn = this;

    var result;
    if (!arr){
        result = context.fn();
    }else{
        var args = [];
        for (var i = 0,len = arr.length;i < len; i++){
            args.push("arr["+ i +"]");
        }
        result = eval("context.fn("+ args + ")");
    }
    delete context.fn;
    return result;
}
```

#### 实现bind

##### 实现bind要做什么

- 返回一个函数，绑定this，传递预置参数
- bind返回的函数可以作为构造函数使用。故作为构造函数时应使得this失效，但是传人的参数依然有效

```javascript
// mdn的实现
if (!Function.prototype.bind) {
  Function.prototype.bind = function(oThis) {
    if (typeof this !== 'function') {
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
    }

    var aArgs   = Array.prototype.slice.call(arguments, 1),
        fToBind = this,
        fNOP    = function() {},
        fBound  = function() {
          // this instanceof fBound === true时,说明返回的fBound被当做new的构造函数调用
          return fToBind.apply(this instanceof fBound
                 ? this
                 : oThis,
                 // 获取调用时(fBound)的传参.bind 返回的函数入参往往是这么传递的
                 aArgs.concat(Array.prototype.slice.call(arguments)));
        };

    // 维护原型关系
    if (this.prototype) {
    }
    // 下行的代码使fBound.prototype是fNOP的实例,因此
    // 返回的fBound若作为new的构造函数,new生成的新对象作为this传入fBound,新对象的__proto__就是fNOP的实例
    fBound.prototype = new fNOP();

    return fBound;
  };
}
详解请移步JavaScript深入之bind的模拟实现 #12

模拟Object.create
Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__。
// 模拟 Object.create

function create(proto) {
  function F() {}
  F.prototype = proto;

  return new F();
}
```

#### **模拟Object.create**

##### Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的___proto___.

```javascript
// 模拟 Object.create
function create(proto){
	function F(){
		F.prototype=protp;
		
		return new F()
	}
}
```

#### **解析 URL Params为对象**

 ```
let url = 'http://www.domain.com/?user=anonymous&id=123&id=456&city=%E5%8C%97%E4%BA%AC&enabled';
parseParam(url)

/* 结果
{ user: 'anonymous',
  id: [ 123, 456 ], // 重复出现的 key 要组装成数组，能被转成数字的就转成数字类型
  city: '北京', // 中文需解码
  enabled: true, // 未指定值得 key 约定为 true
}
*/
 ```

#### 转化为驼峰，命名

```javascript
let s1= "get-element-by-id"
//转化为 getElementById

let f = function(s){
	return s.replace(/-\w/g,function(x){
		return x.slice(1).toUpperCase();
	})
}
```

#### 源自：https://juejin.im/post/5e100cdef265da5d75243229

#### 声明：文章著作权归作者所有，如有侵权，请联系删除。