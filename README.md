# springboot-auto-deployment
# Maven插件wagon-maven-plugin自动化部署Java项目到Linux远程服务器；

完整 pom 文件

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>demo</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <extensions>
            <extension>
                <groupId>org.apache.maven.wagon</groupId>
                <artifactId>wagon-ssh</artifactId>
                <version>3.2.0</version>
            </extension>
        </extensions>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.example.demo.DemoApplication</mainClass>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>wagon-maven-plugin</artifactId>
                <version>1.0</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <fromFile>target/demo-0.0.1-SNAPSHOT.jar</fromFile>
                    <url>scp://root:1234@10.59.98.27/home</url>
                    <commands>
                        <command>pkill -f demo-0.0.1-SNAPSHOT.jar</command>
                         <!--<command>export JAVA_HOME=/usr/local/jdk1.8.0_91</command>-->
                        <command>
                            <![CDATA[ nohup java -jar /home/demo-0.0.1-SNAPSHOT.jar > /home/nohup.out 2>&1 & ]]></command>-->
                        <command><![CDATA[netstat -nptl]]></command>
                        <command><![CDATA[ps -ef | grep java | grep -v grep]]></command>
                    </commands>
                    <displayCommandOutputs>true</displayCommandOutputs>
                </configuration>
            </plugin>

        </plugins>
    </build>

</project>

```



target/xxx.jar 这里是相对pom.xml的位置，改一改jar包名字就ok

 <url><scp://user:password@192.168.20.128/home></url>

 这里是远程服务器的用户名、密码、ip、jar包存放位置

> <commands>
>  <command>pkill -f demo-0.0.1-SNAPSHOT.jar</command>
>  <command>nohup java -jar /home/demo-0.0.1-SNAPSHOT.jar > /home/nohup.out 2>&1 &</command>
>  </commands>

这里是远程服务器复制完JAR包以后执行的命令，这里先kill掉跑起来的jar包进程，再后台启动一个。

#### maven 命令

> mvn clean package -DskipTests wagon:upload-single wagon:sshexec
>  然后可能会输入一下yes/no初次链接服务器验证

