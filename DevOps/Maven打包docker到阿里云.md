### Maven setting.xml

```
<server>
  <id>docker-registry</id>
  <username>76216170@qq.com</username>
  <password>123456</password>
  <configuration>
    <email>76216170@163.com</email>
  </configuration>
</server>
```

### Pom 文件配置

```
<properties>
  <docker.plugin.version>1.1.0</docker.plugin.version>
  <docker.server.id>docker-registry</docker.server.id>
  <cola.registry.name>cuifeng</cola.registry.name>
  <cola.registry.url>registry.cn-qingdao.aliyuncs.com</cola.registry.url>
</properties>

<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>${springboot.version}</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <!--docker maven插件-->
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>${docker.plugin.version}</version>
                <configuration>
                    <imageName>${cola.registry.url}/${cola.registry.name}/${project.artifactId}:${project.version}</imageName>
                    <dockerDirectory>${project.basedir}</dockerDirectory>
                    <resources>
                        <resource>
                            <targetPath>/</targetPath>
                            <directory>${project.build.directory}</directory>
                            <include>${project.build.finalName}.jar</include>
                        </resource>
                    </resources>
                    <registryUrl>${cola.registry.url}</registryUrl>
                    <serverId>${docker.server.id}</serverId>
                    <pushImage>true</pushImage>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

### Dockerfile

```
FROM openjdk:8u292-jdk-slim
MAINTAINER xiaolifeizei@163.com

RUN mkdir -p /cola-web-domain
WORKDIR /cola-web-domain

EXPOSE 8085
EXPOSE 20889

ADD ./target/*.jar ./app.jar
ENV PARAMS=""

ENTRYPOINT ["sh","-c","java -Djava.security.egd=file:/dev/./urandom $PARAMS -jar  app.jar"]
```

### 配置Maven命令

```
clean package docker:build -DpushImage -Dmaven.test.skip=true
```

### 登录阿里云Docker Registry

```
docker login --username=*******@qq.com registry.cn-qingdao.aliyuncs.com
```
