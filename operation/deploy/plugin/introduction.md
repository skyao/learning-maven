# maven deploy plugin

> 注： 内容翻译自 [Apache Maven Deploy Plugin](https://maven.apache.org/plugins/maven-deploy-plugin/)

deploy 插件主要用在 deploy 阶段，添加 artifact 到远程仓库来和其他开发者和项目分享。这通常在集成或者发布环境中完成。它也可以用于部署一个特殊的 artifact （例如第三方jar）。

因为仓库包含比 artifact 更多的内容(poms, 原数据，MD5和SHA1散列文件...)，部署意味着不仅要复制 artifact，而且要保证所有这些信息正确更新。这是 deploy 插件的责任。

为了工作，部署要求：

- 仓库的信息： 它的地址，访问它的传输方法(FTP, SCP, SFTP...) 和可选的用户特定要求的账户信息
- artifact的信息： group，artifact，version，packaing，classifier...
- 部署者：实际执行部署的方法。可以实现为 wagon transport (使之跨平台)，或者使用系统特定方法

这些信息将来自隐含的pom文件和命令行。settings.xml 文件也可能解析来获取用户认证。

## Goal概述

deploy 插件有两个 goal：

- [delpoy:deploy](https://maven.apache.org/plugins/maven-deploy-plugin/deploy-mojo.html) 用于自动安装 artifact， 它的 pom 和特别项目产生的附带的 artifact 。即使不是全部也是大多数的和部署相关的信息存储在项目的pom文件中。
- [deploy:deploy-file](https://maven.apache.org/plugins/maven-deploy-plugin/deploy-file-mojo.html) 用于安装单个 artifact 和它的pom。在这种场景下 artifact 信息可以来自可选的指定pom文件，也可以通过使用命令行来完成/覆盖。

## 使用

如何使用 deploy 插件的一般注意事项可以在 [使用](usage.md)页面中找到。更多特别的使用案例在下面给出的例子中有描述。最后但不是不重要，用户偶尔在 [插件的wiki页面](http://docs.codehaus.org/display/MAVENUSER/Deploy+Plugin)贡献额外的例子，tips或者勘误。

## 例子

为了提供对 deploy 插件使用的更好理解， 可以看一下下面的例子：

项目部署：

- [使用FTP部署](deployment_with_ftp.md)
- [使用外部SSH部署](deploy-ssh-external.md)

