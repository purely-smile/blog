title: centos7 配置vmtool

categories: linux

---

### 前言

1. 本文配置是基于[这篇](http://www.ytyzx.net/index.php?title=%E5%A6%82%E4%BD%95%E5%9C%A8Linux%28CentOS_7%29%E5%91%BD%E4%BB%A4%E8%A1%8C%E6%A8%A1%E5%BC%8F%E5%AE%89%E8%A3%85VMware_Tools&variant=zh-cn)文配置的，可能是我安装的centos是精简安装的，好多开发环境没安装，所以安装vmtool的时候多了一个错误，所以这里整理了下重新发布，也方便自己以后重新配置。

2. 参考文章写的很详细所以本文就简单的命令行步骤了，只是加上我的理解，和参考文章未提到的两处错误的处理。

3. 本文教程是在root用户下安装的，如果非root用户，请在命令行前加`sudo`!

4. 如果多行命令，则为逐行执行。

5. 安装前建议拍摄快照，以防不测。

### 安装

1. 右键centos，选择安装vmtool（我刚安装过了，所以变成了取消安装）

> ![](../../../images/QQ截图20161106192401.jpg)

2. 然后创建cdrom文件夹，用于第三步的挂载cdrom。
```bash
mkdir /mnt/cdrom
```

3. 挂载光盘到上一部创建的目录里。
```bash
mount -t iso9660 /dev/cdrom /mnt/cdrom
```
4. 然后复制`/mnt/cdrom`目录的文件到根目录(vmtool的版本会根据vm安装版本不同而不一样，所以一般选择到这个目录，按tab选择已有版本即可)
```bash
cp /mnt/cdrom/VMwareTools-9.9.0-2304977.tar.gz /root/vm.tar.gz
```

5. 在根目录解压缩
```bash
cd //回到根目录
tar -xzf vm.tar.gz //解压缩
```
6. 安装配置vmtool的环境（如果不安装可能会出错）,其中最后一个是参考教程里没有提到的，但是我安装的时候会报错，所以加入了。报错信息`What is the location of the "ifconfig" program on your machine?`,详情见参考资料2；

```bash
yum -y install perl gcc make kernel-headers kernel-devel net-tools
```

7. 安装vmtool，逐步输入以下代码，然后一路回车即可。
```bash
cd vmware-tools-distrib //进入目录
./vmware-install.pl  // 安装
```

8. 如果一路不出错，也都成功安装了，所以不出所料，又报错了，错误信息如下`The path "" is not a valid path to the 3.10.0-327.el7.x86_64 kernel headers.`，解决方法如下（详情看参考资料3）
```bash
 yum install kernel-headers-`uname -r` kernel-devel-`uname -r`
 yum install audit
 yum install gcc make binutils
```
9. 以上全部安装完成，重启虚拟机，然后重新打开目录安装即可。
```bash
reboot
cd vmware-tools-distrib
 ./vmware-install.pl
```

10. 安装成功，最后提示`Enjoy,--the VMware team`

11. 最后建议安装完成后保存个快照，方便以后回退。不至于重头再来。




### 参考资料

1. [如何在Linux(CentOS 7)命令行模式安装VMware Tools](http://www.ytyzx.net/index.php?title=%E5%A6%82%E4%BD%95%E5%9C%A8Linux%28CentOS_7%29%E5%91%BD%E4%BB%A4%E8%A1%8C%E6%A8%A1%E5%BC%8F%E5%AE%89%E8%A3%85VMware_Tools&variant=zh-cn);

2. [VMware Tools cannot be installed on RHEL 7 due to missing ifconfig (2075519)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2075519);

3. [path to kernel header](https://ask.fedoraproject.org/en/question/9667/path-to-kernel-header/);