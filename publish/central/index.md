# 发布到中央仓库



## 注册Sonatype 用户

注册地址：

https://issues.sonatype.org/secure/Signup!default.jspa

切记要记住用户名密码，后面都要用到。

```bash
email:aoxiaojian@gmail.com
Fullname: Sky Ao
Username: skyao
password: $$$$$$$$$
```

## 创建Issue

为了发布我们的构件，需要以提交issue的方式创建ticket,地址为：

https://issues.sonatype.org/secure/CreateIssue.jspa?issuetype=21&pid=10134

内容参考：

https://issues.sonatype.org/projects/OSSRH/issues/OSSRH-35916

备注：

1. group id 可以是`io.openfoundation`这种，然后所有的子域名就都可以用
2. 需要提供信息，说明域名是自己所有的

提交之后等待答复，因为时差原因，一般是隔天。

如果审批通过，会有回复，同时issue状态修改为 resolved。

## 发布准备

### 生成gpg秘钥

```bash
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y
```

然后会要求输入信息：

```bash
Real name：Sky Ao
Email address:aoxiaojian@gmail.com
Comment:aoxiaojian
```

再要求输入密码`You need a Passphrase to protect your secret key.`为了简单起见，设置为和sonatype登录账号一致。

之后有个奇怪的环节：

```bash
gpg: gpg-agent is not available in this session
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

Not enough random bytes available.  Please do some other work to give
the OS a chance to collect more entropy! (Need 242 more bytes)
```

只好乱敲一顿键盘，哗哗哗。

```bash
gpg: /home/sky/.gnupg/trustdb.gpg: trustdb created
gpg: key 732796B4 marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   2048R/732796B4 2017-11-16
      Key fingerprint = DD69 ###
uid                  Sky Ao$$$
sub   2048R/400AA044 2017-11-16
```

总算生成了，验证一下：

```bash
gpg --list-keys
/home/sky/.gnupg/pubring.gpg
----------------------------
pub   2048R/732796B4 2017-11-16
uid                  Sky Ao (aoxiaojian) <aoxiaojian@gmail.com>
sub   2048R/400AA044 2017-11-16
```

### 发布gpg公钥

```bash
gpg --keyserver hkp://pool.sks-keyservers.net --send-keys 732796B4
gpg: sending key 732796B4 to hkp server pool.sks-keyservers.net
```

验证公钥是否发布成功：

```bash
gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 732796B4
gpg: requesting key 732796B4 from hkp server pool.sks-keyservers.net
gpgkeys: key 732796B4 not found on keyserver
gpg: no valid OpenPGP data found.
gpg: Total number processed: 0
gpg: keyserver communications error: key not found
gpg: keyserver communications error: bad public key
gpg: keyserver receive failed: bad public key
```

这里很无耻的失败了，看错误提示是没有找到资料，但是提交时没有报错。反复几次也是如此，最后灵机一动，是不是要翻墙？

```bash
hp gpg --keyserver hkp://pool.sks-keyservers.net --send-keys 732796B4
gpg: sending key 732796B4 to hkp server pool.sks-keyservers.net

hp gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 732796B4
gpg: requesting key 732796B4 from hkp server pool.sks-keyservers.net
gpg: key 732796B4: "Sky Ao (aoxiaojian) <aoxiaojian@gmail.com>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1
```

这次终于对了，走http proxy就可以发布成功，验证也通过了。

### 修改maven设置

修改maven的全局配置文件settings.xml，增加下面的内容，用户名密码为Sonatype上面注册的用户名和密码：

```xml
<servers>
    <server>
        <id>oss</id>
        <username>skyao</username>
        <password><![CDATA[ao$$$$$]]></password>
    </server>
</servers>
```

然后修改项目的pom.xml文件，加入需要的信息：

```xml
<name>Foundation Dependencies</name>
<description>Dependency definitions for all foundation projects</description>
<url>https://github.com/openfoundation/foundation-dependencies/</url>
<organization>
    <name>Open Foundation</name>
    <url>http://openfoundation.io</url>
</organization>
<licenses>
    <license>
        <name>Apache License, Version 2.0</name>
        <url>http://www.apache.org/licenses/LICENSE-2.0</url>
    </license>
</licenses>
<developers>
    <developer>
        <name>Sky Ao</name>
        <email>aoxiaojian@gmail.com</email>
    </developer>
</developers>
<scm>
    <connection>scm:git:git@github.com:openfoundation/foundation-dependencies.git</connection>
    <developerConnection>scm:git:git@github.com:openfoundation/foundation-dependencies.git</developerConnection>
    <url>git@github.com:openfoundation/foundation-dependencies.git</url>
</scm>
```

再增加一个profile，名为`oss`：

```xml
<profiles>
    <profile>
        <id>oss</id>
        <build>
            <plugins>
                <!-- Source -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-source-plugin</artifactId>
                    <version>${maven-source-plugin.version}</version>
                    <executions>
                        <execution>
                            <phase>package</phase>
                            <goals>
                                <goal>jar-no-fork</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
                <!-- Javadoc -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-javadoc-plugin</artifactId>
                    <version>${maven-javadoc-plugin.version}</version>
                    <executions>
                        <execution>
                            <phase>package</phase>
                            <goals>
                                <goal>jar</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
                <!-- GPG -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-gpg-plugin</artifactId>
                    <version>${maven-gpg-plugin.version}</version>
                    <executions>
                        <execution>
                            <phase>verify</phase>
                            <goals>
                                <goal>sign</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
        <distributionManagement>
            <snapshotRepository>
                <id>oss</id>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            </snapshotRepository>
            <repository>
                <id>oss</id>
                <url>https://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
            </repository>
        </distributionManagement>
    </profile>
</profiles>
```

特别注意：snapshotRepository 与 repository 中的 id 一定要与 setting.xml 中 server 的 id 保持一致。这里我们都设置为oss。

## 上传构件到OSS

执行下面maven发布命令，注意指定profile为pom.xml里面对应的`oss`：

```bash
mvn clean deploy -P oss
```

mvn命令执行中，在每个构件上传时，都会要求输入密码，这个密码就是前面gpg加密用的密码：

```bash
Enter passphrase: gpg: gpg-agent is not available in this session
```

看到一下提示信息，说明deploy成功：

```bash
Uploading: https://oss.sonatype.org/content/repositories/snapshots/io/openfoundation/dependencies/foundation-dependencies-standard/0.1.0-SNAPSHOT/maven-metadata.xml
Uploaded: https://oss.sonatype.org/content/repositories/snapshots/io/openfoundation/dependencies/foundation-dependencies-standard/0.1.0-SNAPSHOT/maven-metadata.xml (816 B at 0.6 KB/sec)
```

此时构件已经部署在oss仓库中，但还不是中央仓库。

## 发布

在下里面地址登录，用户名和密码就是在sonatype注册的：

https://oss.sonatype.org/

在Staging Repositories找到自己的仓库，可以用模糊搜索。

> 备注：切记是要发布release版本的构件，不能是snapshot，不然不会出现在Staging Repositories里面。

然后close/release，OK。

第一次发布时，需要回到issue页面回复。然后等待审批。

### 验证中央仓库

同样上述步骤完成10分钟之后就可以同步到maven中央仓库，可以通过直接浏览仓库的方式做验证，比如访问下列地址：

http://repo1.maven.org/maven2/io/openfoundation/

## gpg错误处理

mvn命令执行中，在每个构件上传时，都会要求输入密码，注意错误信息：

```bash
Enter passphrase: gpg: gpg-agent is not available in this session
```

但是在命令行中已经有gpg-agent命令了。gogole之后发现是版本问题：

https://askubuntu.com/questions/860370/gpg-agent-cant-be-reached

首先安装gpg2：

```bash
sudo apt install gpgv2
```

然后在maven的settings.xml中加入两个属性，主要要在激活的profile里面：

```bash
<profile>
	<properties>
        <gpg.executable>gpg2</gpg.executable>
        <gpg.useagent>true</gpg.useagent>
        <gpg.passphrase><![CDATA[ao###]]></gpg.passphrase>
	</properties>
</profile>
```

再次执行mvn命令：

```bash
mvn clean deploy -P oss
```

这是就换成gpg2了，但是依然不能直接免密码输入，还是会有一个弹窗要求输入密码，不过总算可以选择保存密码了。参考下文：

- [Avoid gpg signing prompt when using Maven release plugin](https://stackoverflow.com/questions/14114528/avoid-gpg-signing-prompt-when-using-maven-release-plugin)

TBD：还是需要找到更好的办法。

## 参考资料

参考：https://www.dexcoder.com/selfly/article/4352


