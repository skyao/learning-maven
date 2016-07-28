# Goals

这个插件的 goal有：

| Goal | 描述 |
|--------|--------|
| deploy:deploy |     部署artifact到远程仓库   |
| deploy:deploy-file  |   在远程仓库安装artifact     |
| deploy:help  |    显示帮助信息    |

## 使用

在project的插件配置中指定版本

```xml
<project>
  ...
  <build>
    <!-- To define the plugin version in your parent POM -->
    <pluginManagement>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
        ...
      </plugins>
    </pluginManagement>
    <!-- To use the plugin goals in your POM or parent POM -->
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-deploy-plugin</artifactId>
        <version>2.8.2</version>
      </plugin>
      ...
    </plugins>
  </build>
  ...
</project>
```