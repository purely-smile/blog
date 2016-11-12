title: operation not supported on socket, symlink

categories: node

---

### 前言

>使用exprss做服务器的时候，为了保持和生产环境一样的centos环境。所以开发环境也是用的vm tool开的共享目录。如何配置vm tool可以参考这篇我这篇文章。[配置vmtool](/2016/11/06/web/node/centos-vm-tool/)

### 问题

>但是在虚拟机安装模块的时候，经常会报错。如下
```bash
ENOTSUP: operation not supported on socket, symlink '/usr/lib/node_modules/qrcode' -> '/mnt/hgfs/PangeaBridge/node_modules/qrcode'
```

> 经查询问题在于在虚拟机环境下无法在共享目录下链接文件目录引起的

### 解决方法

1. 添加 -no-bin-links 命令即可。
```bash
npm rebuild qrcode -no-bin-links
```

### 分析

1. 为什么安装的时候加入 -no-bin-links都可以正常安装了。经查询发现-no-bin-links 的作用就是为了不创建符号链接。[参考这里](https://segmentfault.com/q/1010000006163452)

### 参考资料

1. [npm syscall symlink error -95 when installing node-sass on Docker for Windows](http://stackoverflow.com/questions/37062847/npm-syscall-symlink-error-95-when-installing-node-sass-on-docker-for-windows);

2. [npm install --no-bin-links中的参数“no-bin-links”表示什么意思？](https://segmentfault.com/q/1010000006163452);
