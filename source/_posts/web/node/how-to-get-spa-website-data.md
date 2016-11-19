title: 使用node如何爬去单页面应用SPA的数据

categories: node

---

### 前言

> 前几天一个学大数据的朋友找我帮忙抓取一个[科研资源库](http://webofknowledge.com)的部分数据，由于也是第一次写爬虫，然后分析了下这个网站的结构。该网站使用的是angular1构建的，页面的数据都是通过ajax加载的。刚好我擅长的也是angular，所以决定试一下。

### 使用工具

1. [request(必须)](https://www.npmjs.com/package/request)：用来获取网站数据；

2. [json2csv(非必须)](https://www.npmjs.com/package/json2csv)：用来把获取到的内容转换成csv文件；

### 完整代码

```js
var request = require('request');
var co = require('co');
let fs = require('fs');
// request.debug = true;
var j = request.jar();

let headers = {
  'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.87 Safari/537.36',
  'Accept': 'application/json, text/plain, */*',
  'Accept-Encoding': 'gzip, deflate, br',
  'Accept-Language': 'en',
  'Connection': 'keep-alive',
  'Content-Type': 'application/json;charset=UTF-8',
  'Referer': 'https://incites.thomsonreuters.com/',
  'Origin': 'https://incites.thomsonreuters.com',
  'Host': 'incites.thomsonreuters.com',
};

let listUrl = 'https://incites.thomsonreuters.com/incites-app/explore/0/subject/data/table/page';

let itemUrl = 'https://incites.thomsonreuters.com/incites-app/drilldowns/0/subject/dbd_54/data?key=-7411569751019892909&skip=0&take=50000';


co(function*() {
  let listData = {
    url: listUrl,
    headers,
    gzip: true,
    json: { "take": 77, "skip": 0, "sortBy": "sbjName", "sortOrder": "desc", "indicators": ["key", "seqNumber", "sbjName", "rank", "wosDocuments", "norm", "timesCited", "percentCited"], "filters": { "schema": { "is": "China SCADC Subject 77 Narrow" }, "assprsnIdTypeGroup": { "is": "name" }, "assprsnIdType": { "is": "fullName" }, "personIdTypeGroup": { "is": "name" }, "personIdType": { "is": "fullName" } }, "pinned": [] }
  };

  let list = yield post(listData);
  let { items } = list;


  for (let i = 0; i < items.length; i++) {
    let item = items[i];
    let { key, sbjName } = item;
    let url = `https://incites.thomsonreuters.com/incites-app/drilldowns/0/subject/dbd_54/data?key=${key}&skip=0&take=50000`;

    let itemData = {
      url,
      headers,
      gzip: true,
      json: { "filters": { "schema": { "is": "China SCADC Subject 77 Narrow" }, "assprsnIdTypeGroup": { "is": "name" }, "assprsnIdType": { "is": "fullName" }, "personIdTypeGroup": { "is": "name" }, "personIdType": { "is": "fullName" } }, "pinned": [] }
    };
    let itemList = yield post(itemData);
    console.log(sbjName, 'success');
    fs.writeFileSync(`./down/${sbjName}.json`, JSON.stringify(itemList.items), 'utf8');
  }

});



function post(data, down) {
  let { url } = data;
  'JSESSIONID=xxxx6287B56DFB0FC5E443E39BC4FE; USERNAME="xxxxx@qq.com"; PSSID="xxxxxx2FaIyLcSifJExxkoNROqgXDTTL6-18x2dcOoZ3e81goxx6ZPQtUZbfUAx3Dx3DVBtxxOi675Q34L2Wex2BLAQTgx3Dx3D-iyiHxxh55B2RtQWBj2LEuawx3Dx3D-1iOubBm4x2FSwJjjKtx2F7lAaQx3Dx3D"; CUSTOMER="NINGBO UNIV"; E_GROUP_NAME="IC2 Platform"; SECCONTEXT=8ada020de2a4e9a0d01408a3069bc7793398035adcbb08856c6f0bfa677e6155553039af5df0de285d2e131d54578abfb7e2397e2569fba348ed65017dc662e9cb648df955fd1e4702a15b42a74315481aad6bb1e6838f943212677bc9e3e0924642ac88729c154b5d5968f1ee5cc0e65cd189dacc6cd951aef256c2ba5f844d4f74d4ee9432acd9bc81998e4ca90301b01f1cc1468ec3fa3986f4c031db027513199593aea994b401dce0f805941fa2b9f0512650a0c3f631d3f8eaf98b8eda352876d1f677ead0c65d0503f5dafa96b7924a78f7384ecdd0e682e37ec87713e55b85f53e559ba693c2493c70f7c4259f35a8e5f3ca02f02eecddfa111658e1f7d8095f0a4fb8ba954ac3ba069193394e56eb1262ff17df98bb78893451086219a1129a6013861daf7c63175f564cbbafbc02c97be3480bb36433165bf7dbeb5ec39fba711f3e44f92043bf25d3a585fe837e786a6a5b6ae297aae41195c8bc'.split(' ').forEach(val => {
    var cookie = request.cookie(val);
    j.setCookie(cookie, url);
  });
  data.jar = j;
  return new Promise((resolve, reject) => {
    request.post(data, (err, res, body) => {
      resolve(body);
    });
  });

}
```

### 分析

> 这个文件会在一个页面中加载一个类别列表，然后点击响应类型的具体数据。

1. 使用浏览器（我用的chrome，其他浏览器都行），先打开F12控制台，然后打开需要抓取的类别页，切换到network选项卡，可以看到page请求项返回的数据就是页面加载内容，如下图：

![](/images/QQ截图20161119112119.jpg),然后报错请求的地址。对应上述代码的变量`listUrl`；

2. 然后复制下request请求的header必要信息，保存在上述代码`headers`中，用以伪造浏览器请求。

3. 然后就是cookie的数据了，请求数据必须携带cookie才能通过服务器的认证，参考上述代码`post`函数里面对应cookie的处理。

4. 然后复制请求体里的post内容，对应上述代码`listData`中post的内容，需要说明的是网站请求的数量是take=25，但是我们可以修改这里的数值为我们需要的数量。

5. 然后调用post函数获取类别列表，然后遍历获取类别对应的id（类别的id用于向对方服务器请求具体的内容）

6. 然后继续分析类别详情请求的内容，具体还是重复上述步骤

7. 最后把获取到的数据，保存为json文件即可。

8. 如果需要csv文件的话，也可以同时处理。我是爬去全部内容后，然后才转换的。具体代码如下：
```js
var json2csv = require('json2csv');
let fs = require('fs');

let soucePath = './down/'; // 数据路径
let targePath = './csvFiles'; //目标路径

fs.exists(targePath, (exists) => {
    if (!exists) {
        fs.mkdir(targePath, 777);
    }
});

var files = fs.readdirSync(soucePath); // 读取元数据目录文件列表

if (!files[0]) {
    return console.log(`${soucePath}目录下不存在文件`);
}

files.forEach(file => {
    let path = soucePath + file;
    let csvPath = targePath + '/' + file.split('.')[0] + '.csv';
    let baseData = fs.readFileSync(path);
    baseData = JSON.parse(baseData);
    console.log('正在转换:', csvPath);
    let csvData = baseData.map(csv => {
        let { a } = csv;
        let { title } = a;
        csv.title = title;
        return csv;
    });
    let fields = Object.keys(csvData[0]);
    let csvResult = json2csv({ data: csvData, fields });

    fs.writeFileSync(csvPath, csvResult, 'utf8');
});
```

### 遇到的cookie坑
1. request使用cookie，按照request的文档来的，文档代码如下
```js
request.cookie('key1=value1');
```
一开始我也是直接把cookie全部丢进去了，但是请求的时候对方服务器就提示我需要身份认证

2. 但是我在chrome 复制该请求的curl bash请求内容，然后执行是可以拿到数据的，说明只是cookie没正常提交。

3. 然后本机开了个node server，使用cookie-parse中间件处理后打印携带的cookie，然后只有第一条有效。

4. 然后把请求链接调整为我们项目user-get的地址，发现也是提示需要认证。至此排除了对方服务器对认证的特殊验证，

5. 然后google request cookie 相关内容，然后并没有发现。

6. 打算放弃的时候，想起来去request 的github issue中检索，发现了有人说setCookie一次只能设置一个，然后恍然，具体实现参考以上post函数的内容