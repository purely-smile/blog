title: js function 作用域问题

categories: 前端

---

### 前言
1. 今天有朋友发我一段如下一段代码，问我执行结果。然后想当然以为结果是1;

2. 随后发现踩坑了。首先这段代码在严格模式下是有错误的，所以以下分析是在非严格模式下。

```js
 a = 10;
 (function a() {
     a = 1;
     console.log(a); // 正确答案是function a 自己
 })();
```

### 错误原因

1. 忽略了函数的名称，以为是匿名函数。
```js
 a = 10;
 (function() {
     a = 1;
     console.log(a); // 正确输出为1
 })();

```

### 分析问题

1. 首先`function a`定义的是函数表达式，所有函数名称a的作用范围只有函数本身，不能在函数体外访问到，例如:
```js
var a = function b(){
    console.log(b);
};
console.log(b);  // Uncaught ReferenceError: b is not defined
a(); // function b(){console.log(b)};
```
> 分析：首先把函数b复制给变量a，此时函数b为函数表达式。然后打印b的时候提示错误，错误的原因在于函数表达式b的function name只有在函数体内有效。也就是递归调用有效。 所有如果运行a的时候，会打印出b的函数内容，证明函数表达式b有效作用域为函数内。

2. 函数体内遇到同名变量的微妙变化，首先：
```js
function a(){
    a=10;
    console.log(a);
}
a(); // 10
```
以上代码可以分为三部分函数执行前，执行中，执行后。
>1. 函数执行前如下,原因是在浏览器环境全局函数默认挂载在window对象上了。
```js
a === window.a;  //true
```
>2. 函数执行中，以上函数等价于如下代码
```js
function a(){
    window.a = 10; // a覆盖了自身函数的声明体
    console.log(a);
}
```
>3. 既然a已经被10覆盖了，所以打印出来的自然是10了

其次，如下代码
```js
(function a(){
    a = 10;
    console.log(a);
})();
```
以上代码是IIFE（自执行函数）,所以只分析执行中即可，以上代码可以转换为
```js
(function a(){
    window.a === undefined; //true
    window.a = 10;
    console.log(a);
})();
```
> 分析：首先function a还是我们第一条分析的。命名函数表达式的名称只能函数内部访问，所以`window.a = 10;`执行之前是不存在a这个全局变量的。然后执行后，再次打印a，由于函数作用域的原因，访问的是函数a自身，因为函数体内存在变量a的索引，所以就没有向上级作用域（也就是全局作用域查找）;

其实以上代码可以再变换一下，如下(函数体内添加了变量a的声明)
```js
(function a(){
    var a = 10;
    console.log(a);
})();
```
> 分析：这次代码多了个变量的声明。然后在函数体定义了变量a，所以打印结果为10；

### 总结

1. 命名函数表达式的名称只对其函数内部有效;

2. 变量赋值尽量尽量避免与函数名称、或者函数表达式名称一致;

3. 变量需要显式的声明使用;

4. 使用严格模式编码，已减免错误的发生。

