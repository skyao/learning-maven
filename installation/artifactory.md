# 安装artifactory

## 准备JDK

安装前需要确保JAVA_HOME有正确设置，可以修改 /etc/environment，加入JAVA_HOME：

	JAVA_HOME=/usr/lib/jvm/java-8-oracle

最新版本的artifactory会要求至少jdk1.8版本, 否则无法启动.如果遇到在安装好jdk8之后再安装jdk7一起安装的情况, 需要额外执行一次命令:

	sudo apt-get install oracle-java8-set-default

## 下载安装

从[https://www.jfrog.com/open-source/](https://www.jfrog.com/open-source/)下载到最新的artifactory，将zip包解压。

> 注： 在linux上如果用wget命令直接下载会报错404，需要用浏览器先下载，得到具体下载地址之后再用wget命令下载。

将目录复制到/usr/lib，执行安装:

```bash
sudo mv artifactory*** /usr/lib/
cd /usr/lib/
sudo mv artifactory*** artifactory
cd artifactory/bin
sudo ./installService.sh

sudo systemctl start artifactory.service
```

安装成功后就可以通过[http://localhost:8081](http://localhost:8081)访问artifactory的页面了，默认管理员账号和密码为admin/password。

- [参考地址](http://www.softwarepassion.com/install-artifactory-on-ubuntu-box/)

## 安装设置

安全起见，登录后先修改admin密码，点击Admin -> Security -> Users -> User List -> admin，修改密码即可。

另外，Admin -> Security -> `General Security Configuration`, 取消"Allow Anonymous Access"。

## nginx反代

为了方便访问，避免输入8081这样的端口号，考虑使用nginx做反代。

1. 在`/etc/nginx/sites-available`目录下创建文件`maven.dreamfly.io`

    ```json
    server {
           listen 80;

           server_name maven.dreamfly.io;

           location /
           {
                  proxy_pass http://127.0.0.1:8081;
                  proxy_redirect      http://127.0.0.1:8081 $scheme://maven.dreamfly.io;
           }
    }
    ```

2. 在`/etc/nginx/sites-enabled`目录下创建文件`maven.dreamfly.io`

	```bash
    sudo ln -s ../sites-available/maven.dreamfly.io maven.dreamfly.io 
    sudo service nginx restart
    ```

3. 设置`maven.dreamfly.io`的域名解析到目标IP地址

## 设置maven

需要在maven的setttings文件中加入如下的 profile 设置， `sudo vi /usr/share/maven3/conf/settings.xml`：

```xml
<profile>
    <id>dreamfly</id>
    <activation>
        <activeByDefault>false</activeByDefault>
    </activation>
    <repositories>
        <repository>
            <id>dreamfly-release</id>
            <name>Dreamfly Release Repository</name>
            <releases>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
                <checksumPolicy>warn</checksumPolicy>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <url>http://maven.dreamfly.io/artifactory/libs-release</url>
            <layout>default</layout>
        </repository>

        <repository>
            <id>dreamfly-snapshot</id>
            <name>Dreamfly Snapshot Repository</name>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>always</updatePolicy>
                <checksumPolicy>warn</checksumPolicy>
            </snapshots>
            <url>http://maven.dreamfly.io/artifactory/libs-snapshot</url>
            <layout>default</layout>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>dreamfly-plugin-release</id>
            <name>Dreamfly Plugin Release Repository</name>
            <releases>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
                <checksumPolicy>warn</checksumPolicy>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <url>http://maven.dreamfly.io/artifactory/plugins-release</url>
            <layout>default</layout>
        </pluginRepository>

        <pluginRepository>
            <id>dreamfly-snapshot</id>
            <name>Dreamfly Plugin Snapshot Repository</name>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>always</updatePolicy>
                <checksumPolicy>warn</checksumPolicy>
            </snapshots>
            <url>http://maven.dreamfly.io/artifactory/plugins-snapshot</url>
            <layout>default</layout>
        </pluginRepository>
    </pluginRepositories>
</profile>
```

然后在activeProfiles中激活:

```xml
<activeProfiles>
    <activeProfile>dreamfly</activeProfile>
</activeProfiles>
```

## deploy设置

再在`<servers>`中加入账号信息，注意id保持一致：

```xml
<servers>
    ......
    <server>
      <id>dreamfly</id>
      <username>admin</username>
      <password>dreamfly.io</password>
    </server>
</servers>
```

这样，在deploy时，就可以通过`-DaltDeploymentRepository`参数指定发布的目标地址，然后配合设置文件中的账号信息，完成deploy。

```bash
mvn deploy -DaltDeploymentRepository=dreamfly::default::http://maven.dreamfly.io/artifactory/libs-snapshot
```
