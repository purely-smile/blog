title: 学习js踩过的坑

categories: 前端

---

### 问题列表及解决方法

1. cookie 记录的boolean值会默认转化为字符串，所有做判断的时候需要注意。

2. 跨域post数据，请求头需要设置withCredentials:true，如果需要同时提交cookie，则服务器端也需要设置,以下是node跨域配置。
```js
const cors = require('cors');
app.use(cors({
  origin:'http://localhost:8080',
  credentials: true
}));
```



### 参考资料
1. [cors npm配置](https://www.npmjs.com/package/cors)