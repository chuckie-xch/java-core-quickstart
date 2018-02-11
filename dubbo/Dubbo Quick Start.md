# Dubbo Quick Start

## Dubbo Architecture



[![dubbo-architecture.png](https://i.loli.net/2018/02/11/5a7fe774e020d.png)](https://i.loli.net/2018/02/11/5a7fe774e020d.png)

`Dubbo`提供了三个关键的功能：

* 远程调用接口
* 容错处理，负载均衡
* 服务自动注册和发现



## Quick Start

####  环境要求：

* JDK: 1.6+
* Maven: 3.0+



#### 创建父工程dubbo

添加`maven`依赖：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.bestcode.study</groupId>
    <artifactId>dubbo</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <name>dubbo quick start Maven Webapp</name>
    <packaging>pom</packaging>

    <properties>
        <dubbo.version>2.6.0</dubbo.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>dubbo</artifactId>
                <version>${dubbo.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <finalName>dubbo</finalName>
    </build>
</project>
```



#### 定义服务接口

由于服务消费者和服务提供者都依赖于服务接口，所以强烈推荐，将接口层独立出一个模块。

##### 创建服务接口模块dubbo-api

##### 创建服务接口DemoService:

```java
package com.bestcode.study.dubbo.api.service;

/**
 * dubbo demo service
 *
 * @author <a href="mailto:1205241831@qq.com">Xch</a>
 * @version 1.00
 * @see
 * @since 2018.02.11
 */
public interface DemoService {

    String sayHello(String name);
}

```



##### 创建服务提供者模块dubbo-provider

##### 实现DemoService，提供服务

```java
package com.bestcode.study.dubbo.provider.service.impl;

import com.bestcode.study.dubbo.api.service.DemoService;

/**
 * DemoService实现
 *
 * @author <a href="mailto:1205241831@qq.com">Xch</a>
 * @version 1.00
 * @see
 * @since 2018.02.11
 */
public class DemoServiceImpl implements DemoService {

    public String sayHello(String name) {
        return "hello " + name;
    }
}
```



##### 配置服务提供者（Spring）





