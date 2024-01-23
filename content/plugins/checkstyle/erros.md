---
title: "checkstyle常见错误"
linkTitle: "常见错误"
weight: 100
date: 2021-01-29
description: >
  使用 Maven checkstyle 插件时经常遇到的错误
---



### cannot initialize module Header

报错如下：

```bash
$ mvn checkstyle:checkstyle   
[INFO] Scanning for projects...
[INFO] Inspecting build with total of 1 modules...
[INFO] Installing Nexus Staging features:
[INFO]   ... total of 1 executions of maven-deploy-plugin replaced with nexus-staging-maven-plugin
[INFO] 
[INFO] --------------------------< io.dapr:dapr-sdk >--------------------------
[INFO] Building dapr-sdk 1.10.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- checkstyle:3.1.1:checkstyle (default-cli) @ dapr-sdk ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.849 s
[INFO] Finished at: 2023-09-12T04:11:07Z
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-checkstyle-plugin:3.1.1:checkstyle (default-cli) on project dapr-sdk: An error has occurred in Checkstyle report generation.: Failed during checkstyle configuration: cannot initialize module RegexpHeader - illegal value '.java_header' for property 'headerFile': com.puppycrawl.tools.checkstyle.api.CheckstyleException: Unable to find: .java_header -> [Help 1]
```

出现这个错误的原因是执行 `mvn checkstyle:checkstyle` 或者执行其他maven命令如 `mvn install` 时，所在的目录不是 maven 项目的根目录，

参考：

- https://github.com/FINRAOS/DataGenerator/issues/109

  > I'm guessing that ${basedir} won't work from the submodule directories because it uses the closest pom file (whose basedir is the submodule directory).
  
- https://issues.apache.org/jira/browse/CALCITE-2788

  > The reason for this exception is that the checker.xml use a location related to the parent project for the header file,however the header file is not located in the sub-project.

解决方法： 

1. 在 maven 项目的根目录下执行
2. 如果需要在子项目中单独执行命令，可以通过添加 `-Dcheckstyle.skip=true` 来跳过 checkstyle，如 `mvn install -Dcheckstyle.skip=true`
