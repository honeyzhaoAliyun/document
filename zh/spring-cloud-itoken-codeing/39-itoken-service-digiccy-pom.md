# itoken-service-digiccy-pom
## pom.xml 
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.vvdd</groupId>
        <artifactId>itoken-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../itoken-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>itoken-service-digiccy</artifactId>
    <packaging>jar</packaging>

    <name>itoken-service-digiccy</name>
    <url>http://www.vvdd.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <dependency>
            <groupId>com.vvdd</groupId>
            <artifactId>itoken-common-service</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <repositories>
        <repository>
            <id>nexus</id>
            <name>Nexus Repository</name>
            <url>http://172.23.34.220:8081/repository/maven-public/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <releases>
                <enabled>true</enabled>
            </releases>
        </repository>
    </repositories>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.vvdd.itoken.service.digiccy.ServiceDigiccyApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```