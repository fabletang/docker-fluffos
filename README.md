Docker Fluffos
==============
### 项目概要 

此项目参考了 https://github.com/lostsnow/docker-fluffos ,在此基础上找到最终解决方法。<br> 
fluffos版本为:3.0-alpha9.0<br> 
docker版本为:1.12.6<br> 
docker基础系统为:ubuntu:14.04<br> 

### build compile image

  建立为编译fluffos源码的docker镜像,确定已经安装了docker 1.12.+/git。以下的命令可能需要
su root 或者 sudo。
```bash
mkdir mudserver
cd mudserver
git clone https://github.com/fabletang/docker-fluffos
cd docker-fluffos
docker build -t fable/fluffos-build ./build
```
成功后,docker images 可以看到  REPOSITORY下 显示fable/fluffos-build  。

### compile driver

  下载fluffos源码
```bash
cd ..
cd mudserver 
git clone https://github.com/fluffos/fluffos.git
```
  编译fluffos源码，生成mudlib的驱动, 最终产生包括driver的3个二进制。 
XXXX替换为相应的目录.

```bash
docker run --rm -v /XXXX/mudserver:/opt/projects fable/fluffos-build
```
成功后，ls -la docker-fluffos/bin/,可以看到三个二进制文件，注意日期，是否新生成的。
(因为此项目已经包括编译好的二进制，当然你也可以直接使用,忽略以上步骤。^_^)

### build driver image

  生成fluffos驱动镜像,实际就是把 3个二进制打包进docker, 同时安装其运行需要的lib库。
```bash
docker build -t fable/fluffos ./docker-fluffos
``` 
成功后,docker images 可以看到  REPOSITORY下 显示fable/fluffos 。

### run mudlib

  运行mud,即用floffs驱动mudlib,比如fy4/xkx等(https://github.com/mudchina/有一些mudlib)。
以下的 fy4 为参考,可能需要修改源码。
```bash
cd mudserver
mkdir mudlib
cd mudlib
git clone https://github.com/huangleon/fy4.git
cp mudserver/docker-fluffos/example/fy4.ini fy4/fy4.ini
docker run -d --name fy4 \
    -p 8888:8888 \
    -v XXX/mudserver/mudlib/fy4/:/opt/projects \
    fable/fluffos /opt/projects/fy4.ini
```
成功后，docker ps,可以看到"0.0.0.0:8888->8888/tcp", 失败可以 docker logs -f fy4.
也可以进一步查看mudlib的debug.log. (比如tail -f mudserver/mudlib/fy4/log/debug.log.)
