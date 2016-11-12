title: js map 不起作用

categories: js

---

### 前言

>获取到服务器json数据后，想着使用js map重新加工数据的时候，总是偶尔发现map有时候不起作用，之前也没一直认真分析下原因,这次抽空分析了下。

### 错误代码
```js
let a = [1,2,3];
a.map( val => val+1 );
console.log(a); // [1,2,3]  ,可我期待得到[2,3,4]
```
### 正确代码
```js
let a = [1,2,3];
let b =  a.map( val => val+1)
console.log(b);  //[2,3,4]
```

### 错误原因
经测试发现，通过map遍历后会得到新数组，但是map并不会修改也有的数组，所以问题就出在了对map认知的偏差造成的。

### 参考文档

1. [Array.prototype.map()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
