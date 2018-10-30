---
title: 谈一谈maven项目搭建
date: 2017-07-29
categories: java
---

# 使用springBoot框架搭建

springBoot有以下几个特点：
- 内嵌Tomcat
- 简化maven配置
- 自动配置spring

由于本人是一个后台小白，因此该项目的搭建使用的是`start.spring.io`，同时选择了mybatis和web项目的依赖，该网站可以自动生成一个maven项目，并且还可以选择你想要的依赖。

pom.xml文件中的依赖如下：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jersey</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>   
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-rest</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <!-- mybatis -->
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>1.2.0</version>
    </dependency>
    <dependency>
        <groupId>commons-dbcp</groupId>
        <artifactId>commons-dbcp</artifactId>
        <version>1.4</version>
    </dependency>
    <!-- 该依赖用于和数据库连接 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jta-narayana</artifactId>
    </dependency>
</dependencies>
```

# 初始化src/main/java

经有经验的java程序员指导，了解到main/java文件夹下的几个主要的包

- com.xxx.config ：配置类
- com.xxx.mapper ：和数据库交互的*mapper.java 
- com.xxx.entity : 实体
- com.xxx.service ：服务层
- com.xxx.serviceImpl ：实现
- com.xxx.rest ：控制层，用于交互
- com.xxx.项目名称 ：存放的是项目的入口文件

由于项目中要使用mybatis，因此需要以下两个配置类：
1、MybatisConfiguration.java  实现TransactionManagementConfigurer接口

```java
@Configuration
@EnableTransactionManagement
public class MybatisConfiguration implements TransactionManagementConfigurer{
    
    @Autowired
    private DataSource dataSource;

    @Bean(name = "sqlSessionFactory")
    public SqlSessionFactory sqlSessionFactoryBean() {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        //设置实体类所在的包
        bean.setTypeAliasesPackage("com.spring.entity");
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        
         try {
                
                // 设置数据读取的xml文件的路径
                bean.setMapperLocations(resolver.getResources("classpath:/mapper/*.xml"));
                return bean.getObject();
            } catch (Exception e) {
                e.printStackTrace();
                throw new RuntimeException(e);
            }
    }
    
    @Bean
    public PlatformTransactionManager annotationDrivenTransactionManager() {
        return new DataSourceTransactionManager(dataSource);
    }

}

```
2、MyBatisMapperScannerConfig.java

```java
@Configuration
//MybatisConfiguration  是上一个类的名称
@AutoConfigureAfter(MybatisConfiguration.class)
public class MyBatisMapperScannerConfig {
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        mapperScannerConfigurer.setSqlSessionFactoryBeanName("sqlSessionFactory");
        //设置mapper类所在的包
        mapperScannerConfigurer.setBasePackage("com.spring.mapper");
        return mapperScannerConfigurer;
    }
}
```

在我创建的项目中，数据库的处理上使用的是jdbc，所以在config包下需要加入DataSourceConfiguration.java

```java
@Configuration
@PropertySource("classpath:application.properties")
public class DataSourceConfiguration {
    @Value("${jdbc.driver-class-name}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;
    @Value("${jdbc.max-idle}")
    private int maxIdel;
    @Value("${jdbc.min-idle}")
    private int minIdle;
    @Value("${jdbc.max-wait-millis}")
    private long maxWait;
    @Value("${jdbc.initial-size}")
    private int initialSize;
    @Value("${jdbc.max-active}")
    private int maxActive;
    
    @Value("${jdbc.validation-query}")
    private String validationQuery;
    
    @Value("${jdbc.test-on-borrow}")
    private boolean testOnBorrow;
    @Value("${jdbc.test-while-idle}")
    private boolean testWhileIdle;
    
    @Value("${jdbc.time-between-eviction-runs-millis}")
    private long timeBetweenEvictionRunsMillis;
    
    /**
     * 此处设置数据源的信息与连接池的操作
     * @return
     */
    @Bean
    public BasicDataSource dataSource(){
        BasicDataSource dataSource = new BasicDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        dataSource.setMaxIdle(maxIdel);
        dataSource.setMinIdle(minIdle);
        dataSource.setMaxWait(maxWait);
        dataSource.setMaxActive(maxActive);
        dataSource.setInitialSize(initialSize);
        dataSource.setValidationQuery(validationQuery);
        dataSource.setTestOnBorrow(testOnBorrow);
        dataSource.setTestWhileIdle(testWhileIdle);
        dataSource.setTimeBetweenEvictionRunsMillis(timeBetweenEvictionRunsMillis);
        
        return dataSource;
    }
}
```

只是这样配置还是不够的，还需要在main/source文件夹下进行application.properties配置

```
server.context-path=/
spring.main.banner-mode=console

spring.application.name=teststudy
#spring.jersey.type=filter

#spring.datasource.url=jdbc:mysql://localhost:3306/info_default
#spring.datasource.username=root
#spring.datasource.password=admin
#spring.datasource.driver-class-name=com.mysql.jdbc.Driver
#spring.datasource.max-idle=10
#spring.datasource.max-wait=10000
#spring.datasource.min-idle=5
#spring.datasource.initial-size=5
#spring.datasource.validation-query=SELECT 1
#spring.datasource.test-on-borrow=false
#spring.datasource.test-while-idle=true
#spring.datasource.time-between-eviction-runs-millis=18800
#
##spring.datasource.type = org.apache.commons.dbcp2.BasicDataSource
#

jdbc.driver-class-name=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8&useSSL=false
jdbc.username=root
jdbc.password=admin
jdbc.max-idle=120
jdbc.max-wait-millis=10000  
jdbc.min-idle=5
jdbc.initial-size=10
jdbc.max-active=2335
jdbc.validation-query=SELECT 1
jdbc.test-on-borrow=false
jdbc.test-while-idle=true
jdbc.time-between-eviction-runs-millis=18800
```

要想成功启动项目，还需要有本地数据库。

至此，项目搭建基本完成。

# 编写代码

## 定义数据库文件 StudyMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.spring.mapper.StudyMapper">
    <select id="StudyGet" resultType="com.spring.entity.TestStudyEntity">
        select * from test where name=#{name}
    </select>
    
    <insert id="StudyPost" parameterType="com.spring.entity.TestStudyEntity">
        insert into test (name) values (#{name})
    </insert>
    
    <update id="StudyPut" parameterType="com.spring.entity.TestStudyEntity">
        update test set name=#{name}
    </update>
    
    <delete id="StudyDelete">
        delete from test where id=#{id}
    </delete>
</mapper>
```

## mapper  StudyMapper.java

```java
@Repository("studyMapper")
public interface StudyMapper {
    public int StudyPost(TestStudyEntity entity) throws IOException;
    public int StudyDelete(@Param("id") int id) throws IOException;
    public int StudyPut(TestStudyEntity entity) throws IOException;
    public TestStudyEntity StudyGet(@Param("name") String name) throws IOException;
}
```

## entity  TestStudyEntity.java

```java
package com.spring.entity;

public class TestStudyEntity {
    private String name;
    private Integer id;
    
    public String getName(){
        return name;
    }
    
    public void setName(String name){
        this.name = name;
    }
    
    public Integer getId(){
        return id;
    }
    
    public void setId(Integer id){
        this.id = id;
    }
}
```

## service StudyService.java

```java
public interface StudyService {
    public int StudyPost(TestStudyEntity entity) throws IOException;
    public int StudyDelete(int id) throws IOException;
    public int StudyPut(TestStudyEntity entity) throws IOException;
    public TestStudyEntity StudyGet(String name) throws IOException;
}
```

## service.impl StudyServiceImpl.java

```java
@Service
@Transactional
public class StudyServiceImpl implements StudyService{

    @Resource
    private StudyMapper mapper;

    @Override
    public int StudyDelete(int id) throws IOException {
        return mapper.StudyDelete(id);
    }

    @Override
    public int StudyPut(TestStudyEntity entity) throws IOException {
        return mapper.StudyPut(entity);
    }

    @Override
    public TestStudyEntity StudyGet(String name) throws IOException {
        return mapper.StudyGet(name);
    }

    @Override
    public int StudyPost(TestStudyEntity entity) throws IOException {
        return mapper.StudyPost(entity);
    }   
}
```

## rest TestRest.java

```java
@RestController
@RequestMapping("/test")
public class TestRest {
    
    @Autowired
    private StudyService service;
    
    @PostMapping
    @ResponseBody
    public int TestPost(@RequestBody TestStudyEntity study){
        try{
            return service.StudyPost(study);
        }catch(IOException e){
            /**
             * 与数据库通信失败了
             */
        }
        return 0;
    }
    
    @DeleteMapping("/{id}")
    public TestStudyEntity TestDelete(@PathVariable("id") Integer id){
        
        try {
            service.StudyDelete(id);
        } catch (IOException e) {
            /**
             * 
             * 与数据库通信失败0
             */
        }
        return null;
    }
    
    @PutMapping
    public int TestPut(@RequestBody TestStudyEntity study){
        try{
            return service.StudyPut(study);
        }catch(IOException e){
            
        }
        return 0;
    }
    
    @GetMapping
    public TestStudyEntity TestGet(@RequestParam("name") String name){
        
        try {
            return service.StudyGet(name);
        } catch (IOException e) {
            /**
             * 与数据库通信失败
             */
        }
        return null;
    }    
}
```

# 补充

1、在source文件夹下的mapper文件中，存放的是进行数据库操作的xml文件。

2、作为以一个前端，框架指定是不能少的，由于我选用了vue，打算进行前后台分离的开发，前后台项目启动时会产生跨域问题，因此我在入口文件中设置了跨域允许。
在入口文件中加入该方法即可：

```java
@Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurerAdapter() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                //设置允许被跨域访问的来源
                registry.addMapping("/**").allowedOrigins("http://localhost:8000");
            }
        };
    }
```
