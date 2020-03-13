<!-- TOC -->

  - [可以使用哪些关键字声明变（常）量、它们的区别是什么？](#可以使用哪些关键字声明变常量它们的区别是什么)
  - [不使用 const 声明常量](#不使用-const-声明常量)
  - [数组去重](#数组去重)
  - [for、forEach、map 的区别？能否中断循环？](#forforeachmap-的区别能否中断循环)
  - [对象拷贝](#对象拷贝)
  - [闭包](#闭包)
  - [原型](#原型)
  - [作用域链](#作用域链)
  - [this](#this)
  - [跨域](#跨域)
  - [Promise](#promise)
- [交流讨论](#%e4%ba%a4%e6%b5%81%e8%ae%a8%e8%ae%ba)

<!-- /TOC -->
### 可以使用哪些关键字声明变（常）量、它们的区别是什么？

### 不使用 const 声明常量

### 数组去重

> 1. 通过 Set
```javascript
[...new Set(arr)];
```

> 2. 通过 indexOf 与 index 判断
```javascript
arr.filter((item, index) => {
  return arr.indexOf(item) === index;
});
```

> 3. 通过对象的 key 不可重复
```javascript
let obj = {};
arr.map(item => {
  if (obj[item] === undefined) {
    obj[item] = item;
  }
});
Object.keys(obj);
```

### for、forEach、map 的区别？能否中断循环？

> for 为关键字；forEach、map 为方法，map 可以返回新数组
>
> for 可以使用 break、return 中断
>
> forEach、map 不能使用常规方法中断，如果需要，可以使用抛异常的方式中断。

### 对象拷贝

### 闭包

### 原型

### 作用域链

### this

```javascript
var obj = {
    name: 1,
    fun: function () {
      console.log(this.name);
    },
    fun1: () => {
      console.log(this.name);
    }
};

obj.fun(); // 1, this 指向 obj
obj.fun1(); // '' this 指向 window

var fun = obj.fun;
var fun1 = obj.fun1;
fun(); // '' (window 下存在 name 属性)
fun1(); // '' (window 下存在 name 属性)
```

### 跨域

> 跨域是浏览器的同源政策的限制，当协议、域名或端口不一致时会产生跨域
>
> 解决办法：
> JSONP、CORS、postMessage、服务器代理等
>
> JSONP 原理
> 因为 src 允许访问外部资源，JSONP 是通过动态创建 script 标签，请求地址设为 src。因此 JSONP 只可以进行 GET请求。
> 数据通过 callback 函数传递。需要在前端提前定义好相同回调名的函数接收数据。

```javascript
function callback (data) {
  // dosomething
}

// JSONP 响应
callback(data);
```

### Promise



## 交流讨论

欢迎关注公众号【前端develop】

<img src="../img/wechat-publicity.png" width="400px">