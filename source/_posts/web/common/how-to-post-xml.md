title: node-request如何post提交xml数据

categories: 前端

---

### 问题描述

> 最近接口微信公众号的时候，需要使用node接口，post提交xml数据。之前一直没接触过xml提交数据（前后端一直用的都是json），然后发现把xml格式的数据，放到请求体data里面，是无法正常提交数据的，google了下，也没找到如何提交。

### 解决问题

> 几经尝试，发现使用body:xml，格式可以正常提交到微信服务器获取数据，代码如下：

```js
  request.post({
    url: 'https://api.mch.weixin.qq.com/pay/unifiedorder',
    body: xml
  }, (err, httpResponse, body) => {
    console.log(body);
  });

```