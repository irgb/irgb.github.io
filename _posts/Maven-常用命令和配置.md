---
title: Maven 常用命令和配置
date: 2017-04-16
categories: maven
tags: [maven]
---

## 打加入依赖的包

在 pom.xml 中加入 `maven-shade-plugin` 插件：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.0.0</version>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                    	 <minimizeJar>true</minimizeJar>
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

可以看到 target 目录下包含两个 jar 包：

- my-app-1.0-SNAPSHOT.jar : with dependencies     
- original-my-app-1.0-SNAPSHOT.jar : without dependencies

**Attention**

- 上面的配置同时指定了程序的入口`com.example.app.Main`, 执行命令 `java -jar ./target/my-app-1.0-SNAPSHOT.jar` 可以执行 jar 包。
- minimizeJar 配置的作用是最小化 jar 包，即只将程序依赖的 classes 文件加入 jar 包中，这会大大减小包的大小。
- [还可以利用 `maven-assembly-plugin` 打包](https://www.mkyong.com/maven/create-a-fat-jar-file-maven-assembly-plugin/)，但这个插件不能解决依赖同一个包的多版本时的冲突

**Reference**

- [Create a fat Jar file – Maven Shade Plugin](http://www.mkyong.com/maven/create-a-fat-jar-file-maven-shade-plugin/)
- [Apache Maven Shade Plugin - shade:shade](http://maven.apache.org/plugins/maven-shade-plugin/shade-mojo.html)

## mvn 命令常用参数

- `-Dmaven.test.skip=true` : 即不编译 test 代码，也不运行单元测试。
- `-Dmaven.javadoc.skip=true` : 不生成 javadoc 包。