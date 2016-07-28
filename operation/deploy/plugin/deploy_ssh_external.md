# 在外部SSH命令中部署

为了使用SSH部署artifact，必须首先在pom的 distributionManagement 元素中指定使用的 SSH 服务器，并在 build 元素中指定 extension：

```xml
<project>
  ...
  <distributionManagement>
    <repository>
      <id>ssh-repository</id>
      <url>scpexe://repository.mycompany.com/repository</url>
    </repository>
  </distributionManagement>

  <build>
    <extensions>
      <!-- Enabling the use of SSH -->
      <extension>
        <groupId>org.apache.maven.wagon</groupId>
         <artifactId>wagon-ssh-external</artifactId>
         <version>1.0-beta-6</version>
      </extension>
    </extensions>
  </build>
  ..
</project>
```

如果从Unix或者有已安装的 Cygwin 中部署，不需要在 settings.xml 中有任何额外的配置，因为所有东西都将从环境中获取。但是如果在windows上并使用类似plink，将需要下面的设置：


```xml
<settings>
  ...
  <servers>
    <server>
      <id>ssh-repository</id>
      <username>your username in the remote system if different from local</username>
      <privateKey>/path/to/your/private/key</privateKey> <!-- not needed if using pageant -->
      <configuration>
        <sshExecutable>plink</sshExecutable>
        <scpExecutable>pscp</scpExecutable>
        <sshArgs>other arguments you may need</sshArgs>
      </configuration>
    </server>
  </servers>
  ...
</settings>
```

在使用maven部署之前确保可以手工登录给定的 SSH 服务器。一旦确认所有的事情都正确搭建，可以使用maven来部署artifact：

```bash
mvn deploy
```

有时可能在部署时有遇到权限问题，如果是这样，可以像这样设置文件和目录权限：

```xml
<settings>
...
<servers>
 <server>
   <id>ssh-repository</id>
   <!--
    |
    | Change default file/dir permissions
    |
    -->
   <filePermissions>664</filePermissions>
   <directoryPermissions>775</directoryPermissions>
   <configuration>
     <sshExecutable>plink</sshExecutable>
     <scpExecutable>pscp</scpExecutable>
   </configuration>
 </server>
</servers>
...
</settings>
```
