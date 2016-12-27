# 设置中央仓库的镜像

tags:镜像

maven默认的 central 中央仓库，指向的地址是 `repo.maven.apache.org` 。

这个地址经常出现问题，比如不能访问，或者速度超慢（低于1k/s）。因此在国内最好找一个国内的镜像站点来避免直接访问 central 中央仓库。

## 阿里云的镜像

阿里云提供的maven中央仓库。

配置方式：修改maven根目录下的conf文件夹中的setting.xml文件，增加mirror，内容如下：

```xml
<mirrors>
	......
    <mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
```

## ~~开源中国的镜像~~

注： 已经不能再使用......