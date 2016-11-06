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

> 注：因为众所周知的原因，下载node速度会很慢，nvm会经常卡在一个进度不跳动，我的解决方法的`ctrl+c`两次取消安装，然后在继续。因为下载是支持断点的，所以并不会重新下载。


### 参考资料

1. [Installing Node.js via package manager](https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager)；

2. [Node Version Manager - Simple bash script to manage multiple active node.js versions](https://github.com/creationix/nvm)