# 安装Maven

## linux 安装

### ubuntu apt-get安装

[参考地址](http://www.sysads.co.uk/2014/05/install-apache-maven-3-2-1-ubuntu-14-04/)，步骤如下：

1. Remove older version
2. Add following lines to sources.list
3. Update Repository and Install
4. Add Symbolic Link

具体执行命令依次如下：

	sudo apt-get remove maven2
	sudo add-apt-repository "deb http://ppa.launchpad.net/natecarlson/maven3/ubuntu precise main"
	sudo apt-get update
	sudo apt-get install maven3
	sudo ln -s /usr/share/maven3/bin/mvn /usr/bin/mvn