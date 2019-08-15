# 如何在linux中挂载oss到本地文件

由于oss与操作系统间一般都是通过api进行交互的，不进行二次开发很难直接接触到oss中文件，前一段时间在系统中用到获取视频的长度，按照之前的实现方式需要将所有oss的文件都下载到本地，然后使用ffmpeg获取视频的长度，oss中上传的文件非常巨大，所以这种方式非常耗时，又无法自动化获取。搜索发现可以将oss的bucket绑定到linux系统中，当做本地文件使用，这就减少了文件的下载，可以直接使用ffmpeg查看视频文件的长度。

下面讲一下如何将oss的bucket挂载到linux中，这里用的linux系统为centos7。

### 1. 下载并安装

```
wget https://github.com/aliyun/ossfs/releases/download/v1.80.5/ossfs_1.80.5_centos7.0_x86_64.rpm
yum -y localinstall ossfs_1.80.5_centos7.0_x86_64.rpm --nogpgcheck
```

### 2. 生成配置文件

```
echo <bucket-name>:<accessKey>:<seceret> > /etc/passwd-ossfs
```

### 3. 设置文件权限

```
chmod 640 /etc/passwd-ossfs
```

### 4. 生成本地目录并挂载

```
mkdir /opt/videos
ossfs channel-hjh /opt/videos/ -ourl=oss-cn-beijing-internal.aliyuncs.com
```

### 5. 查看结果

```
ls /opt/videos
```

查看目录下是否有对应的文件了。

### 6. 创建开机自启脚本

- 在/etc/init.d/目录下建立文件ossfs

```
vim /etc/init.d/ossfs
```

复制如下内容

```
#! /bin/bash
# chkconfig: 2345 10 90
# description: ossfs service
ossfs channel-hjh /opt/videos/ -ourl=oss-cn-beijing-internal.aliyuncs.com
```

- 设置为开机自启

```
chmod a+x /etc/init.d/ossfs
chkconfig ossfs on
```