title: express body-parse error unexpected token n

categories: 前端

---

#### 问题描述

> 使用body-parde中间件Post提交数据，提交内容为空的时候遇到一个兼容性BUG。使用第三方浏览器的时候可以正常使用，但是使用微信内置浏览器访问的时候就会报错了。

#### 问题查找

> 分别获取post提交的请求体内容，发现第三方浏览器post为空的时候，content-length = 0，然而使用微信内置浏览器content-length = 4，然后使用在express 使用req.on 获取提交的内容。发现提交的内容为null，然后null这个字符无法被body-parse正确解析

#### 解决问题

> 如果post内容为空。可以使用get请求，或者post的data传空即可{}

