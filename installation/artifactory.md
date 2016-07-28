# 安装artifactory

## 准备JDK

安装前需要确保JAVA_HOME有正确设置，可以修改 /etc/environment，加入JAVA_HOME：

	JAVA_HOME=/usr/lib/jvm/java-8-oracle

最新版本的artifactory会要求至少jdk1.8版本, 否则无法启动.如果遇到在安装好jdk8之后再安装jdk7一起安装的情况, 需要额外执行一次命令:

	sudo apt-get install oracle-java8-set-default

## 下载安装

从[https://www.jfrog.com/open-source/](https://www.jfrog.com/open-source/)下载到最新的artifactory，将zip包解压。

将目录复制到/usr/lib，执行安装:

	sudo mv artifactory*** /usr/lib/
	sudo mv artifactory*** artifactory
	cd artifactory/bin
	sudo ./installService.sh
	service artifactory check
	sudo service artifactory start

安装成功后就可以通过[http://localhost:8081](http://localhost:8081)访问artifactory的页面了，默认管理员账号和密码为admin/password。

- [参考地址](http://www.softwarepassion.com/install-artifactory-on-ubuntu-box/)

## 安装设置

安全起见，登录后先修改admin密码，点击Admin -> Security -> Users -> User List -> admin，修改密码即可。

另外，Admin -> Security -> General, 取消"Allow Anonymous Access"。

## 设置maven

需要在maven的setttings文件中加入如下的 profile 设置：

```xml
<profile>
    <id>local</id>
    <activation>
        <activeByDefault>false</activeByDefault>
    </activation>
    <repositories>
        <repository>
            <id>local-release</id>
            <name>Local Release Repository</name>
            <releases>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
                <checksumPolicy>warn</checksumPolicy>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <url>http://localhost:8081/artifactory/libs-release</url>
            <layout>default</layout>
        </repository>

        <repository>
            <id>local-snapshot</id>
            <name>Local Snapshot Repository</name>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>always</updatePolicy>
                <checksumPolicy>warn</checksumPolicy>
            </snapshots>
            <url>http://localhost:8081/artifactory/libs-snapshot</url>
            <layout>default</layout>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>local-plugin-release</id>
            <name>Local Plugin Release Repository</name>
            <releases>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
                <checksumPolicy>warn</checksumPolicy>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <url>http://localhost:8081/artifactory/plugins-release</url>
            <layout>default</layout>
        </pluginRepository>

        <pluginRepository>
            <id>local-snapshot</id>
            <name>Local Plugin Snapshot Repository</name>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>always</updatePolicy>
                <checksumPolicy>warn</checksumPolicy>
            </snapshots>
            <url>http://localhost:8081/artifactory/plugins-snapshot</url>
            <layout>default</layout>
        </pluginRepository>
    </pluginRepositories>
    </profile>
```

然后在activeProfiles中激活:

```xml
<activeProfiles>
    <activeProfile>local</activeProfile>
</activeProfiles>
```


