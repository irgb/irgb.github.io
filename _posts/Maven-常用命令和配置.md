---
title: Maven 常用命令和配置
date: 2017-04-16
categories: maven
tags: [maven]
---

### 打加入依赖的包

在 pom.xml 中加入 `maven-shade-plugin` 插件：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                <mainClass>com.example.app.Main</mainClass>
                            </transformer>
                        </transformers>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

在 maven 项目目录下运行命令：

```shell
mvn clean package
```

可以看到 target 目录下包含两个 jar 包，较大的一个是加入了依赖的：

- my-app-1.0-SNAPSHOT.jar : with dependencies     
- original-my-app-1.0-SNAPSHOT.jar : without dependencies

上面的配置同时指定了程序的入口`com.example.app.Main`, 执行命令 `java -jar ./target/my-app-1.0-SNAPSHOT.jar` 可以执行 jar 包。

> 还可以利用 `maven-assembly-plugin` 打包，但这个插件不能解决依赖同一个包的多版本时的冲突问题。
> 
#### Reference：
- [Create a fat Jar file – Maven Assembly Plugin](https://www.mkyong.com/maven/create-a-fat-jar-file-maven-assembly-plugin/)

### mvn 命令常用参数

- `-Dmaven.test.skip=true` : 即不编译 test 代码，也不运行单元测试。
- `-Dmaven.javadoc.skip=true` : 不生成 javadoc 包。