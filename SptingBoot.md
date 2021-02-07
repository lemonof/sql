# Spring Boot基础

#### java代码配置方式

```java
package com.tort.tortspringboot.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

@Configuration
@PropertySource("classpath:jdbc.properties")
public class JdbcConfig {
    @Value("${jdbc.url}")
    String url;
    @Value("${jdbc.driverClassName}")
    String driverClassName;
    @Value("${jdbc.username}")
    String username;
    @Value("${jdbc.password}")
    String password;

    @Bean
    public DataSource dataSource(){
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(driverClassName);
        druidDataSource.setUrl(url);
        druidDataSource.setUsername(username);
        druidDataSource.setPassword(password);
        return druidDataSource;
    }

}

```

#### SpringBoot属性注入方式

使用@ConfigurationProperties实现SpringBoot配置文件配置读取和应用

- 使用@configurationProperties编写配置项类将配置文件中的配置项设置到对象中

```java
@ConfigurationProperties(prefix = "jdbc")
public class JdbcPropperties {
    private String url;
    private String driverClassName;
    private String username;
    private String password;

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getDriverClassName() {
        return driverClassName;
    }

    public void setDriverClassName(String driverClassName) {
        this.driverClassName = driverClassName;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
 
 @EnableConfigurationProperties(JdbcPropperties.class)放在类名上
    
   @Bean
   public DataSource dataSource(JdbcPropperties jdbcPropperties){
       DruidDataSource druidDataSource = new DruidDataSource();
       druidDataSource.setDriverClassName(jdbcPropperties.getDriverClassName());
       druidDataSource.setUrl(jdbcPropperties.getUrl());
       druidDataSource.setUsername(jdbcPropperties.getUsername());
       druidDataSource.setPassword(jdbcPropperties.getPassword());
       return druidDataSource;
   }
```

- 使用@ConfigurationProperties在方法上面使用

```java
@Bean
    @ConfigurationProperties(prefix = "jdbc")
    public DataSource dataSource(){
        return new DruidDataSource();
    }
```

#### 多个yml文件配置

```yaml
jdbc:
  driverClassName: com.mysql.jdbc.Driver
  url: jdbc:mysql://127.0.0.1:3306/springboot
  username: root
  password: root

#  激活其他配置文件
spring:
  profiles:
    active: abc,bcd

```

- 多个yml文件 命名规则为 application-xxx.yml

#### lombok应用

- 添加插件

![image-20210203222016116](C:\Users\lemon\AppData\Roaming\Typora\typora-user-images\image-20210203222016116.png)

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId> 
</dependency>
```

@Data：自动提供getter、setter、hashCode、equals、toString等方法

@Getter：自动提供getter方法

@Setter：自动提供setter方法

@Slf4j：自动在bean中提供log变量，其实用的是slf4的日志功能

# Spring Boot整合

#### Spring Boot整合-端口和静态资源

- 修改端口

```yaml
#端口号
server:
  port: 80
  
```

- 静态资源文件位置

```java
ResourceProperties.java
    继承的Resource类中
```

![image-20210203223344365](C:\Users\lemon\AppData\Roaming\Typora\typora-user-images\image-20210203223344365.png)



#### Spring Boot整合-spring MVC拦截器

```java
package com.tort.tortspringboot.interceptor;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
@Slf4j
public class MyInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        log.debug("这是MyInterceptor的preHandle方法");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        log.debug("这是MyInterceptor的postHandle方法");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        log.debug("这是MyInterceptor的afterHandle方法");
    }
}

```

```java
package com.tort.tortspringboot.config;

import com.tort.tortspringboot.interceptor.MyInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    //注册拦截器
    @Bean
    public MyInterceptor myInterceptor(){
        return new MyInterceptor();
    }

    //添加拦截器到spring mvc 拦截器链

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(myInterceptor()).addPathPatterns("/*");
    }
}

```

#### Spring Boot整合-事务和连接池

- 事务配置

  1.添加事务相关的启动器依赖，MySQL相关依赖；

  2.编写业务类使用的事务注解@Transactional

- 数据库连接池hikari配置

 ```yaml
spring:
  profiles:
    active: abc,bcd
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/springboot
    username: root
    password: root
    
#日志记录级别
logging:
  level:
    com.tort.tortspringboot: debug
    org.springframework: info
 ```

#### Spring Boot整合-Mybatis

```xml
<dependency>
     <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.1.4</version>
</dependency>
```

```yaml
mybatis:
#  实体类别名包路径
  type-aliases-package: com.tort.tortspringboot.pojo
  #  映射文件路径
  #  mapper-locations: classpath:mapper/*.xml
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

```java
package com.tort.tortspringboot;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
/**
 * 扫描mapper
 */
@MapperScan("com.tort.tortspringboot.mapper")
public class TortSpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(TortSpringbootApplication.class, args);
    }

}

```

#### Spring Boot整合-通用Mapper

```xml
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.1.5</version>
</dependency>
```

- 配置通用mapper扫描（注意：引入的MapperScan包）

```java
package com.tort.tortspringboot;

//import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import tk.mybatis.spring.annotation.MapperScan;

@SpringBootApplication
/**
 * 扫描mapper
 */
@MapperScan("com.tort.tortspringboot.mapper")
public class TortSpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(TortSpringbootApplication.class, args);
    }

}

```

- 实体类

```java
package com.tort.tortspringboot.pojo;

import lombok.Data;
import lombok.extern.slf4j.Slf4j;
import tk.mybatis.mapper.annotation.KeySql;

import javax.persistence.Column;
import javax.persistence.Id;
import javax.persistence.Table;
import java.util.Date;

@Data
//@Slf4j
@Table(name = "tb_user")
public class User {

    @Id
    @KeySql(useGeneratedKeys = true)
    private Long id;
//    @Column(name = "user_name")
    private String userName;
    private String password;
    private String name;
    private String note;
    private Integer age;
    private Date birthday;
    private Date created;
    private Date updated;
}

```

#### Spring Boot整合-Junit测试

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

- 测试注解

```java
@RunWith(SpringRunner.class)
@SpringBootTest
```

```java
package com.tort.tortspringboot.service;

import com.tort.tortspringboot.pojo.User;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.Date;

import static org.junit.jupiter.api.Assertions.*;
@RunWith(SpringRunner.class)
@SpringBootTest
class UserServiceTest {

    @Autowired
    private UserService userService;

    @Test
    void queryById() {
        User user = userService.queryById(1L);
        System.out.println(" user = " + user);
    }

    @Test
    void saveUser() {
        User user = new User();
        int i = (int) (Math.random()*100);
        user.setAge(i);
        user.setUserName("test"+i);
        user.setPassword("test"+i);
        user.setName("test"+i);
        user.setCreated(new Date());
        userService.saveUser(user);
    }
}
```

#### spring boot整合-redis

```yaml
  redis:
    host: 127.0.0.1
    port: 6379
```

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

- redis测试类

```java
package com.tort.tortspringboot.redis;


import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;
import java.util.Set;

@RunWith(SpringRunner.class)
@SpringBootTest
public class RedisTest {
    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void   test(){
        System.out.println("redis连接成功 : " + redisTemplate);
        //string 字符串
//        redisTemplate.opsForValue().set("str","testRedis");
        redisTemplate.boundValueOps("str").set("test");
        System.out.println("str = " +redisTemplate.boundValueOps("str").get());
        System.out.println("str 的大小： " +redisTemplate.boundValueOps("str").size());//大小 11
        System.out.println("str 的大小： " +redisTemplate.boundValueOps("str").get().equals("test"));
        System.out.println("str 的数据类型： " +redisTemplate.boundValueOps("str").getType());

        //hash 散列
        redisTemplate.boundHashOps("h_key").put("name","xm");
        redisTemplate.boundHashOps("h_key").put("age",12);
        //获取所有域
        Set h_key = redisTemplate.boundHashOps("h_key").keys();
        System.out.println("hash散列的所有域： " +h_key);
        //获取所有值
        List h_key1 = redisTemplate.boundHashOps("h_key").values();
        System.out.println("hash散列的所有域的值：" + h_key1);

        //list 列表
        redisTemplate.boundListOps("l_key").remove(0,"b");//移除所有 b
        redisTemplate.boundListOps("l_key").leftPop();
        redisTemplate.boundListOps("l_key").leftPush("c");
        redisTemplate.boundListOps("l_key").leftPush("b");
        redisTemplate.boundListOps("l_key").leftPush("a");
        List l_key = redisTemplate.boundListOps("l_key").range(0, -1);
        System.out.println("list列表中的所有元素：" + l_key);

        //set 集合
        redisTemplate.boundSetOps("s_key").add("a","b","c");
        Set s_key = redisTemplate.boundSetOps("s_key").members();
        System.out.println("set集合中的所有元素： " + s_key);

        //sorted set 有序集合
        redisTemplate.boundZSetOps("z_key").add("c",3);
        redisTemplate.boundZSetOps("z_key").add("a",1);
        redisTemplate.boundZSetOps("z_key").add("b",2);
        Set z_key = redisTemplate.boundZSetOps("z_key").range(0, -1);
        System.out.println("sorted set 有序集合中的所有元素：" + z_key);


        System.out.println("end...");
    }
}

```



















