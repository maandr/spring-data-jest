Spring Data Jest
=========================
[![Apache License 2](https://img.shields.io/badge/license-ASF2-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0.txt)
[![Build Status](https://travis-ci.org/VanRoy/spring-data-jest.svg?branch=master)](https://travis-ci.org/VanRoy/spring-data-jest)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.vanroy/spring-boot-starter-data-jest/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.github.vanroy/spring-boot-starter-data-jest/)

Spring Data implementation for ElasticSearch based on Jest Rest client

Useful to use Spring Data with ElasticSearch cluster accessible only by HTTP ( for example on AWS).

Versions
-----------

|   spring data jest | spring boot | spring data elasticsearch |  jest  | elasticsearch |
|:------------------:|:-----------:|:-------------------------:|:------:|:-------------:|
|  NOT YET RELEASED  |    1.4.x    |       2.0.2.RELEASE       |  2.0.3 |      2.2.0    |
|   1.0.0.RELEASE    |    1.3.x    |       1.3.4.RELEASE       |  1.0.3 |      1.5.2    |


Quick Start with Spring Boot
-------------------------------

### Maven configuration

Add the Maven dependency:

```xml
<dependency>
    <groupId>com.github.vanroy</groupId>
    <artifactId>spring-boot-starter-data-jest</artifactId>
    <version>2.0.0-SNAPSHOT</version>
</dependency>
```

### Application
```java
@SpringBootApplication(exclude = {ElasticsearchAutoConfiguration.class, ElasticsearchDataAutoConfiguration.class})
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### Configuration ( application.yml )
```yml
spring:
    data:
        jest:
            uri: http://localhost:9200
```


Quick Start without Spring Boot
-------------------------------

### Maven configuration

Add the Maven dependency:

```xml
<dependency>
    <groupId>com.github.vanroy</groupId>
    <artifactId>spring-data-jest</artifactId>
    <version>2.0.0-SNAPSHOT</version>
</dependency>
```

### XML Namespace

You can set up repository scanning via xml configuration, which will happily create your repositories.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:elasticsearch="http://www.springframework.org/schema/data/elasticsearch"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.1.xsd http://www.springframework.org/schema/data/elasticsearch http://www.springframework.org/schema/data/elasticsearch/spring-elasticsearch.xsd">

    <!-- Elastic search test node -->
    <elasticsearch:node-client id="client" local="true" cluster-name="#{T(java.util.UUID).randomUUID().toString()}"
                               http-enabled="true" path-data="target/elasticsearchTestData" path-home="src/test/resources/test-home-dir"
                               path-configuration="node-client-configuration.yml"/>


    <!-- ElasticSearch Jest Client -->
    <bean id="jestClient" factory-bean="jestClientFactory" factory-method="getObject" destroy-method="shutdownClient" />

    <bean id="jestClientConfigBuilder" class="io.searchbox.client.config.HttpClientConfig.Builder">
        <constructor-arg type="java.lang.String" value="http://localhost:9200"/>
    </bean>

    <bean id="jestClientConfig" factory-bean="jestClientConfigBuilder" factory-method="build"/>

    <bean id="jestClientFactory" class="io.searchbox.client.JestClientFactory">
        <property name="httpClientConfig" ref="jestClientConfig"/>
    </bean>

    <!-- Elasticsearch Jest Template -->
    <bean id="jestElasticsearchTemplate" class="com.github.vanroy.springdata.jest.JestElasticsearchTemplate">
        <constructor-arg ref="jestClient"/>
    </bean>

</beans>
```