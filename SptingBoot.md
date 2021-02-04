# SpringBoot

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

#### SpringBoot整合端口和静态资源

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



#### Spring Boot整合spring MVC拦截器

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

#### SpringBoot整合-事务和连接池

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

#### SpringBoot整合Mybatis

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



















