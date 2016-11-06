title: 学习php踩过的坑以及和js的差异

categories: php

---

### 问题列表及解决方法

1. php的时间戳精确到秒s，而js到毫秒ms,所以需要做响应的转换。

2. php访问JSON的数据，使用`$value["message"]`；,不能使用`$value.message`!,而js两者都可以使用。

3. js由于闭包的原有可以在函数体内访问外面作用域的变量，而php如果没有global声明变量或者通过参数传入则会报错。例如
```php
// php错误代码
$a = 1;
function test(){
    echo $a;   // error，不能直接方法函数体外的变量
}
// php正确代码
$a = 1;
function test(){
    global $a;
    echo $a;   // 1
}
 ```
 ```js
// js 代码
let a = 1;
function test(){
    console.log(a); // 1，访问函数体外的变量正常。
}
```
4. 使用php返回数据，如果是boolean类型。使用`echo true`是得不到数据的，或者转换为对应的字符串形式，或者使用`echo json_encode(true)`



### 参考资料

1. [PHP - Get bool to echo false when false](http://stackoverflow.com/questions/4948663/php-get-bool-to-echo-false-when-false);



