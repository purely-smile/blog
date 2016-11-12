title: Centos7 配置node开发环境

categories: node

---
> 本文提供直接安装node和使用nvm管理node两种方式（安装node 6）

> 建议先安装开发环境`yum groupinstall 'Development Tools'`

### 直接安装node(详情见参考资料1)
1. 配置安装软件源（自己理解的）
```bash
curl --silent --location https://rpm.nodesource.com/setup_6.x | bash -
```
2. 安装node
```
yum -y install nodejs
```

3. 完成安装后，输入`node`，即可进入node运行环境。

### 使用nvm管理node版本（建议使用）

1. 下载nvm
```bash
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.32.1/install.sh | bash
```
2. 如果提示`-bash: wget: command not found
`，则先安装wget（如果没有提示，则跳过）
```bash
yum install -y wget
```
3. 安装nvm
```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

4. 安装node 6
```bash
nvm install 6.9.1
```

 也可以使用以下命令安装最新版node
```bash
nvm install node
```

5. 然后可以查看已安装的node
```bash
nvm list
```

### 使用cnpm安装模块

>由于国内安装模块速度太慢，所以建议cnpm

1. 安装[cnpm](https://npm.taobao.org/)
```js
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

2. 然后以后安装模块使用cnpm命令即可,支持 npm 除了 publish 之外的所有命令
```js
cnpm install
cnpm install express
```


### 使用yarn管理模块

>最近新出的yarn也挺火的，可以多线程安装模块，所以速度快很多，被很多人推崇。

1. 下载yarn软件 [下载地址](https://yarnpkg.com/en/docs/install);

2. 然后打开cmd窗口。执行以下命令测试是否安装成功；
```bash
yarn --version
```

3. 安装模块
```bash
yarn
```

4. 使用淘宝镜像
```bash
yarn config set registry https://registry.npm.taobao.org
```



> 注：因为众所周知的原因，下载node速度会很慢，nvm会经常卡在一个进度不跳动，我的解决方法的`ctrl+c`两次取消安装，然后在继续。因为下载是支持断点的，所以并不会重新下载。


### 参考资料

1. [Installing Node.js via package manager](https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager)；

2. [Node Version Manager - Simple bash script to manage multiple active node.js versions](https://github.com/creationix/nvm);

3. [淘宝 NPM 镜像](https://npm.taobao.org/);

4. [yarn](https://yarnpkg.com/);

5. [「CYarn」使用 cnpm 源的 Yarn](https://cnodejs.org/topic/57ff0541487e1e4578afb48d);
