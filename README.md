# quakus-first-poc
my first execute and evaluate quarkus.

## Prerequisites

- Azure VM Standard B1ms (1 VCPU, 2 GB Memory)
- Ubuntu 18.04 LTS

on the way, not enough RAM volume, therefore scaled up Azure VM Size Standard A2 (2 VCPU、3.5 GB Memory)

## Install Docker-CE

```
# apt install apt-transport-https ca-certificates curl software-properties-common
# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
# apt-key fingerprint 0EBFCD88
# add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
# apt update
# apt-install docker-ce
```

## Graalvm Install

```
# wget https://github.com/oracle/graal/releases/download/vm-1.0.0-rc13/graalvm-ce-1.0.0-rc13-linux-amd64.tar.gz
# tar zxvf graalvm-ce-1.0.0-rc13-linux-amd64.tar.gz
# rm graalvm-ce-1.0.0-rc13-linux-amd64.tar.gz
# echo 'export PATH=/root/graalvm-ce-1.0.0-rc13/bin:$PATH' >> .bashrc
# source .bashrc
# java -version
openjdk version "1.8.0_202"
OpenJDK Runtime Environment (build 1.8.0_202-20190206132807.buildslave.jdk8u-src-tar--b08)
OpenJDK GraalVM CE 1.0.0-rc13 (build 25.202-b08-jvmci-0.55, mixed mode)
```

## Create Welcome Hoge App

confirm execute javac java command using graalvm.
```
# vi WelcomeHoge.java
# cat WelcomeHoge.java
public class WelcomeHoge {
  public static void main(String[] args) {
    System.out.println("Welcome, Hoge");
  }
}
# javac WelcomeHoge.java
# java WelcomeHoge
Welcome, Hoge
```

## Install Maven

```
# wget http://ftp.tsukuba.wide.ad.jp/software/apache/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz
# tar zxvf apache-maven-3.6.0-bin.tar.gz
# rm apache-maven-3.6.0-bin.tar.gz
/root/apache-maven-3.6.0/bin
# echo 'MAVEN_HOME=/root/apache-maven-3.6.0' >> .bashrc
# echo 'export PATH=$MAVEN_HOME/bin:$PATH' >> .bashrc
# source .bashrc
# mvn -v
Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-24T18:41:47Z)
Maven home: /root/apache-maven-3.6.0
Java version: 1.8.0_202, vendor: Oracle Corporation, runtime: /root/graalvm-ce-1.0.0-rc13/jre
Default locale: en, platform encoding: UTF-8
OS name: "linux", version: "4.18.0-1013-azure", arch: "amd64", family: "unix"
```

## Create Java Maven Project

create or generate fist quarkus project.
```
# mvn io.quarkus:quarkus-maven-plugin:0.11.0:create \
    -DprojectGroupId=jp.acme \
    -DprojectArtifactId=getting-started \
    -DclassName="org.acme.quickstart.GreetingResource" \
    -Dpath="/hoge"
# ls
WelcomeHoge.class  apache-maven-3.6.0  graalvm-ce-1.0.0-rc13
WelcomeHoge.java   getting-started
```

confirm getting-started/pom.xml
```
<?xml version="1.0"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <groupId>jp.acme</groupId>
  <artifactId>getting-started</artifactId>
  <version>1.0-SNAPSHOT</version>
  <properties>
    <surefire-plugin.version>2.22.0</surefire-plugin.version>
    <quarkus.version>0.11.0</quarkus.version>
    <maven.compiler.source>1.8</maven.compiler.source>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-bom</artifactId>
        <version>${quarkus.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-resteasy</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-arc</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-junit5</artifactId>
    </dependency>
    <dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>rest-assured</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-maven-plugin</artifactId>
        <version>${quarkus.version}</version>
        <executions>
          <execution>
            <goals>
              <goal>build</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>${surefire-plugin.version}</version>
        <configuration>
          <systemProperties>
            <java.util.logging.manager>org.jboss.logmanager.LogManager</java.util.logging.manager>
          </systemProperties>
        </configuration>
      </plugin>
    </plugins>
  </build>
  <profiles>
    <profile>
      <id>native</id>
      <activation>
        <property>
          <name>native</name>
        </property>
      </activation>
      <build>
        <plugins>
          <plugin>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-maven-plugin</artifactId>
            <version>${quarkus.version}</version>
            <executions>
              <execution>
                <goals>
                  <goal>native-image</goal>
                </goals>
                <configuration>
                  <enableHttpUrlHandler>true</enableHttpUrlHandler>
                </configuration>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <artifactId>maven-failsafe-plugin</artifactId>
            <version>${surefire-plugin.version}</version>
            <executions>
              <execution>
                <goals>
                  <goal>integration-test</goal>
                  <goal>verify</goal>
                </goals>
                <configuration>
                  <systemProperties>
                    <native.image.path>${project.build.directory}/${project.build.finalName}-runner</native.image.path>
                  </systemProperties>
                </configuration>
              </execution>
            </executions>
          </plugin>
        </plugins>
      </build>
    </profile>
  </profiles>
</project>
```

tiny modify example source code.
```
# vi getting-started/src/main/java/org/acme/quickstart/GreetingResource.java
# cat getting-started/src/main/java/org/acme/quickstart/GreetingResource.java
package org.acme.quickstart;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Path("/hoge")
public class GreetingResource {

    @GET
    @Produces(MediaType.TEXT_PLAIN)
    public String hello() {
        return "hoge\n";    # add \n
    }
}
```

modify test code.
```
# vi getting-started/src/test/java/org/acme/quickstart/GreetingResourceTest.java
# vi getting-started/src/test/java/org/acme/quickstart/GreetingResourceTest.java
package org.acme.quickstart;

import io.quarkus.test.junit.QuarkusTest;
import org.junit.jupiter.api.Test;

import static io.restassured.RestAssured.given;
import static org.hamcrest.CoreMatchers.is;

@QuarkusTest
public class GreetingResourceTest {

    @Test
    public void testHelloEndpoint() {
        given()
          .when().get("/hoge")
          .then()
             .statusCode(200)
             .body(is("hoge\n")); # modify
    }

}
```

