---
title: Maven 常用命令和配置
date: 2017-04-16
categories: maven
tags: [maven]
---

### 打加入依赖的包

在 pom.xml 中加入以下插件：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-assembly-plugin</artifactId>
            <version>2.6</version>
            <configuration>
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
                <archive>
                    <manifest>
                        <mainClass>com.example.app.Main</mainClass>
                    </manifest>
                </archive>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>single</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

在 maven 项目目录下运行命令：

```shell
mvn clean package assembly:single -Dmaven.test.skip=true -Dmaven.javadoc.skip=true
```

可以看到 target 目录下包含两个 jar 包，较大的一个是加入了依赖的。

执行命令 `java -jar /path/to/jar-with-dependencies.jar` 可以运行 jar 包。