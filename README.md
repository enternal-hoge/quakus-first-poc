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

## Start with Hot Roadding

following execute command.
```
# mvn compile quarkus:dev
```

execute other terminal
```
# curl http://localhost:8080/hoge
hoge
```

## Build ant Run binary file

'mvn compile quarkus:dev' terminal Ctrl + C.
execute command, and wait 5 to 10 minuts.
```
# mvn package -Pnative -Dnative-image.docker-build=true
```

Notice. 
I use Azure VM Size : Standard B1ms (1 VCPU, 2 GB Memory)
Perhaps, not enough RAM Size.
```
# There is insufficient memory for the Java Runtime Environment to continue.
# Native memory allocation (mmap) failed to map 210980864 bytes for committing reserved memory.
# An error report file with more information is saved as:
# /project/hs_err_pid6.log
GraalVM 1.0.0-rc12 warning: INFO: os::commit_memory(0x00000000ea5e0000, 210980864, 0) failed; error='Cannot allocate memory' (errno=12)
Error: Image building with exit status 1
```

Chage VM Size : Standard A2 (2 VCPU、3.5 GB Memory)

Retry.
```
# mvn package -Pnative -Dnative-image.docker-build=true
```

Result, No problem.
```
[getting-started-1.0-SNAPSHOT-runner:6]     universe:   1,864.64 ms
[getting-started-1.0-SNAPSHOT-runner:6]      (parse):  17,877.77 ms
[getting-started-1.0-SNAPSHOT-runner:6]     (inline):  12,603.62 ms
[getting-started-1.0-SNAPSHOT-runner:6]    (compile):  92,796.47 ms
[getting-started-1.0-SNAPSHOT-runner:6]      compile: 126,142.67 ms
[getting-started-1.0-SNAPSHOT-runner:6]        image:   7,102.91 ms
[getting-started-1.0-SNAPSHOT-runner:6]        write:   1,452.43 ms
[getting-started-1.0-SNAPSHOT-runner:6]      [total]: 271,459.77 ms
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  05:24 min
[INFO] Finished at: 2019-03-17T03:19:09Z
[INFO] ------------------------------------------------------------------------
```

following target directory.
```
# ls -la target/
total 19812
drwxr-xr-x 14 root root     4096 Mar 17 03:19 .
drwxr-xr-x  4 root root     4096 Mar 17 02:33 ..
drwxr-xr-x  4 root root     4096 Mar 17 02:51 classes
drwxr-xr-x  3 root root     4096 Mar 17 02:34 generated-sources
drwxr-xr-x  3 root root     4096 Mar 17 02:45 generated-test-sources
-rwxr-xr-x  1 root root 20112728 Mar 17 03:19 getting-started-1.0-SNAPSHOT-runner
-rw-r--r--  1 root root    33987 Mar 17 03:14 getting-started-1.0-SNAPSHOT-runner.jar
-rw-r--r--  1 root root     5504 Mar 17 03:14 getting-started-1.0-SNAPSHOT.jar
-rw-r--r--  1 root root    61061 Mar 17 02:57 hs_err_pid6.log
drwxr-xr-x  2 root root     4096 Mar 17 03:14 lib
drwxr-xr-x  2 root root     4096 Mar 17 02:51 maven-archiver
drwxr-xr-x  3 root root     4096 Mar 17 02:34 maven-status
-rw-r--r--  1 root root     4664 Mar 17 03:14 quarkus.log
drwxr-xr-x  2 root root     4096 Mar 17 03:16 reports
drwxr-xr-x  2 root root     4096 Mar 17 02:45 surefire-reports
drwxr-xr-x  6 root root     4096 Mar 17 02:45 test-classes
drwxr-xr-x  2 root root     4096 Mar 17 02:51 transformed-classes
drwxr-xr-x  6 root root     4096 Mar 17 02:51 wiring-classes
drwxr-xr-x  4 root root     4096 Mar 17 02:34 wiring-devmode
```

execute created binary file
```
# ./target/getting-started-1.0-SNAPSHOT-runner
2019-03-17 03:24:01,777 INFO  [io.quarkus] (main) Quarkus 0.11.0 started in 0.004s. Listening on: http://127.0.0.1:8080
2019-03-17 03:24:01,783 INFO  [io.quarkus] (main) Installed features: [cdi, resteasy]
```

confirm using curl command
```
# curl http://localhost:8080/hoge
hoge
```

first curl comannd execuete slowly a little. but it is acceptable.

## Build and Run Docker Image

Build Docker image 
```
# cd getting-started
# docker build -f src/main/docker/Dockerfile -t eternalhoge/quarkus-firsttry:1.0 .
# docker images
REPOSITORY                                  TAG                 IMAGE ID            CREATED             SIZE
eternalhoge/quarkus-firsttry                1.0                 54583f7aeeca        17 seconds ago      125MB
registry.fedoraproject.org/fedora-minimal   latest              f0c38118c459        3 weeks ago         105MB
swd847/centos-graal-native-image-rc12       latest              83f953b63220        4 weeks ago         1.14GB
```

Run Docker Container
```
# docker run -i --rm -p 8080:8080 eternalhoge/quarkus-firsttry:1.0
2019-03-17 03:39:38,596 INFO  [io.quarkus] (main) Quarkus 0.11.0 started in 0.002s. Listening on: http://0.0.0.0:8080
2019-03-17 03:39:38,596 INFO  [io.quarkus] (main) Installed features: [cdi, resteasy]
```

docker container started in 0.002s. Although the standard HDD of Azure VM is not fast, startup is still fast.
alse, first curl comannd execuete is faster than simple binary.

run detach.
```
# docker run -d --rm -p 8080:8080 eternalhoge/quarkus-firsttry:1.0
96269900991cd8b537c4846aa1c9f12caffd974e063908d2bb1fceda8297d3b2
```

confirm docker container status.
```
# docker ps -a
CONTAINER ID        IMAGE                              COMMAND                  CREATED              STATUS              PORTS                    NAMES
96269900991c        eternalhoge/quarkus-firsttry:1.0   "./application -Dqua…"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp   wonderful_jones
```

after stopping the container, can not see the list of containers. 
```
# docker stop 96269900991c
96269900991c
# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

## Quakus executions list and add execution

```
# mvn quarkus:list-extensions
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< jp.acme:getting-started >-----------------------
[INFO] Building getting-started 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- quarkus-maven-plugin:0.11.0:list-extensions (default-cli) @ getting-started ---
[INFO] Available extensions:
[INFO]   * Agroal - Database connection pool (io.quarkus:quarkus-agroal)
[INFO]   * Arc (io.quarkus:quarkus-arc)
[INFO]   * AWS Lambda (io.quarkus:quarkus-amazon-lambda)
[INFO]   * Camel Core (io.quarkus:quarkus-camel-core)
[INFO]   * Camel Infinispan (io.quarkus:quarkus-camel-infinispan)
[INFO]   * Camel Netty4 HTTP (io.quarkus:quarkus-camel-netty4-http)
[INFO]   * Camel Salesforce (io.quarkus:quarkus-camel-salesforce)
[INFO]   * Eclipse Vert.x (io.quarkus:quarkus-vertx)
[INFO]   * Hibernate ORM (io.quarkus:quarkus-hibernate-orm)
[INFO]   * Hibernate ORM with Panache (io.quarkus:quarkus-hibernate-orm-panache)
[INFO]   * Hibernate Validator (io.quarkus:quarkus-hibernate-validator)
[INFO]   * Infinispan Client (io.quarkus:quarkus-infinispan-client)
[INFO]   * JDBC Driver - H2 (io.quarkus:quarkus-jdbc-h2)
[INFO]   * JDBC Driver - MariaDB (io.quarkus:quarkus-jdbc-mariadb)
[INFO]   * JDBC Driver - PostgreSQL (io.quarkus:quarkus-jdbc-postgresql)
[INFO]   * Kotlin (io.quarkus:quarkus-kotlin)
[INFO]   * Narayana JTA - Transaction manager (io.quarkus:quarkus-narayana-jta)
[INFO]   * RESTEasy (io.quarkus:quarkus-resteasy)
[INFO]   * RESTEasy - JSON-B (io.quarkus:quarkus-resteasy-jsonb)
[INFO]   * Scheduler (io.quarkus:quarkus-scheduler)
[INFO]   * Security (io.quarkus:quarkus-elytron-security)
[INFO]   * SmallRye Fault Tolerance (io.quarkus:quarkus-smallrye-fault-tolerance)
[INFO]   * SmallRye Health (io.quarkus:quarkus-smallrye-health)
[INFO]   * SmallRye JWT (io.quarkus:quarkus-smallrye-jwt)
[INFO]   * SmallRye Metrics (io.quarkus:quarkus-smallrye-metrics)
[INFO]   * SmallRye OpenAPI (io.quarkus:quarkus-smallrye-openapi)
[INFO]   * SmallRye OpenTracing (io.quarkus:quarkus-smallrye-opentracing)
[INFO]   * SmallRye Reactive Messaging (io.quarkus:quarkus-smallrye-reactive-messaging)
[INFO]   * SmallRye Reactive Messaging - Kafka Connector (io.quarkus:quarkus-smallrye-reactive-messaging-kafka)
[INFO]   * SmallRye Reactive Streams Operators (io.quarkus:quarkus-smallrye-reactive-streams-operators)
[INFO]   * SmallRye Reactive Type Converters (io.quarkus:quarkus-smallrye-reactive-type-converters)
[INFO]   * SmallRye REST Client (io.quarkus:quarkus-smallrye-rest-client)
[INFO]   * Spring DI compatibility layer (io.quarkus:quarkus-spring-di)
[INFO]   * Undertow (io.quarkus:quarkus-undertow)
[INFO]   * Undertow WebSockets (io.quarkus:quarkus-undertow-websockets)
[INFO]
Add an extension to your project by adding the dependency to your project or use `mvn quarkus:add-extension -Dextensions="name"`
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  6.661 s
[INFO] Finished at: 2019-03-17T03:57:28Z
[INFO] ------------------------------------------------------------------------
```

add camel core extention.
```
# mvn quarkus:add-extension -Dextensions="io.quarkus:quarkus-camel-core"
```


