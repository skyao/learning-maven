# 使用FTP部署

为了使用 FTP 部署 artifact，必须首先在POM文件的 distributionManagement 元素中指定使用的 FTP 服务器，并在 build 元素中指定 extension：

```xml
<project>
  ...
  <distributionManagement>
    <repository>
      <id>ftp-repository</id>
      <url>ftp://repository.mycompany.com/repository</url>
    </repository>
  </distributionManagement>

  <build>
    <extensions>
      <!-- Enabling the use of FTP -->
      <extension>
        <groupId>org.apache.maven.wagon</groupId>
         <artifactId>wagon-ftp</artifactId>
         <version>1.0-beta-6</version>
      </extension>
    </extensions>
  </build>
  ...
</project>
```

settings.xml 文件需要包含一个 server 元素，这个元素的 id 要和上面的POM中指定的 FTP 仓库的id匹配：

```xml
<settings>
  ...
  <servers>
    <server>
      <id>ftp-repository</id>
      <username>user</username>
      <password>pass</password>
    </server>
  </servers>
  ...
</settings>
```

在使用maven部署之前确保可以手工登录给定的 FTP 服务器。一旦确认所有的事情都正确搭建，可以使用maven来部署artifact：

```bash
mvn deploy
```

