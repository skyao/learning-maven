# 安装Maven

## linux 安装

### ubuntu apt-get安装

[参考地址](http://www.sysads.co.uk/2014/05/install-apache-maven-3-2-1-ubuntu-14-04/)，执行命令依次如下：

```bash
sudo apt-get remove maven2
sudo add-apt-repository "deb http://ppa.launchpad.net/natecarlson/maven3/ubuntu precise main"
sudo apt-get update
sudo apt-get install maven3
sudo ln -s /usr/share/maven3/bin/mvn /usr/bin/mvn
```

执行命令`mvn --version`验证安装：

```bash
Apache Maven 3.2.1 (ea8b2b07643dbb1b84b6d16e1f08391b666bc1e9; 2014-02-14T17:37:52+00:00)
Maven home: /usr/share/maven3
Java version: 1.8.0_151, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-8-oracle/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-042stab120.16", arch: "amd64", family: "unix"
```