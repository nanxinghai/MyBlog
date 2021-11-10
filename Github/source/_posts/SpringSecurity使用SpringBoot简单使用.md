---
title: SpringSecurity使用SpringBoot简单使用（一）
date: 2021-10-20 08:40:21
tags: "SpringSecurity"
cover: https://s3.bmp.ovh/imgs/2021/10/76cc75b3d304959e.jpg
---

### SpringSecurity使用SpringBoot简单使用（一）

先来回顾下最开始我们不用框架时是怎么做认证授权的，

#### 1.不用框架认证流程

> 1.客户端发送请求，controller层将请求对象封装成认证对象User
>
> 2.controller层调用service层，传入参数User对象
>
> 3.service层传入User对象调用Dao层，执行认证逻辑，把Dao层返回的对象封装成一个UserContext（用户详情信息），并且存到session中.利用session.setAttribute("一个常量",userContext对象);

问题：不同用户的session会被覆盖吗？

答：这个取决于sessionId，服务器中会有多个session，通过sessoinId找到相对应的session。如果同一个浏览器发送请求，那么sessionId是相同的，那么session就会被覆盖。如果是不同浏览器就不会覆盖

当用户再次请求时，通过自定义拦截器（自己写个类实现HandlerInterceptor），拦截session中是否存在对象，如果没有就返回登录页面，如果有就拿出session对象，并且对比用户要访问的url与用户的权限，如果有权限就放行，如果没有权限就拦截

代码参考下面

```java
@Component
public class SimpleAuthenticationInterceptor implements HandlerInterceptor {
    //请求拦截方法 
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object
            handler) throws Exception {
        //读取会话信息
        Object object = request.getSession().getAttribute(UserDto.SESSION_USER_KEY);
        if (object == null) {
            writeContent(response, "请登录");
        }
        UserDto user = (UserDto) object;
        //请求的url
        String requestURI = request.getRequestURI();
        if (user.getAuthorities().contains("p1")&&requestURI.contains("/r1")) {
            return true;
        }
        if (user.getAuthorities().contains("p2")&&requestURI.contains("/r2")) {
            return true;
        }
        writeContent(response, "权限不足，拒绝访问");
        return false;
    }

    //响应输出 
    private void writeContent(HttpServletResponse response, String msg) throws IOException {
        response.setContentType("text/html;charset=utf-8");
        PrintWriter writer = response.getWriter();
        writer.print(msg);
        writer.close();
    }
}
```

#### SpringSecurity简单入门案列

创建SpringBoot项目

##### 1.pom文件导入依赖

```xml
<!--springboot项目-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.3.RELEASE</version>
</parent>

<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>

<dependencies>
    <!--web开发依赖-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- 以下是>spring security依赖-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
</dependencies>
```

##### 2.配置文件

```yaml
server:
  port: 8080
  servlet:
    context-path: /security-springboot
spring:
  application:
    name: security-springboot
```

##### 3.启动类（忽略）

##### 4.创建SpringSecurity配置类，并交给Spring管理

```java
// springsecurity的配置文件
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

    // 配置用户信息服务（把用户信息查出来）
    @Bean
    @Override
    protected UserDetailsService userDetailsService() {
        // 基于内存查询
        InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
        manager.createUser(User.withUsername("zhangsan").password("123").authorities("p1").build());
        manager.createUser(User.withUsername("lisi").password("456").authorities("p2").build());
        return manager;
    }

    //  配置密码比对器规则
    @Bean
    public PasswordEncoder passwordEncoder() {
        return NoOpPasswordEncoder.getInstance(); // 不进行密码加密
    }

    //配置安全拦截机制
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .antMatchers("/r/r1").hasAnyAuthority("p1") //想访问路径r/r1，必须拥有权限p1
                .antMatchers("/r/r2").hasAnyAuthority("p2") // 想访问路径r/r2，必须拥有权限p2
                .antMatchers("/r/**").authenticated() // 所有带/r/**的路径必须经过认证  (注意：路径匹配规则必须从小到大，不然不生效)
                .anyRequest().permitAll()                         // 其他请求都放行
                .and()
                .formLogin()   //允许表单登录
                .successForwardUrl("/loginsuccess"); // 表单登录成功后访问地址
    }


}
```

##### 5.编写WebMVC配置类（这个不是必要的）

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    // 控制登录页路径(SpringSecurity默认提供/login为登录页面)
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("redirect:/login");
    }
}
```

写这个类只是为了访问项目根路径/，就可以访问登录页面。也可以不写，只要在访问登录页面时，加上/login就行

##### 6.编写登录成功controller，并加上权限mapping

```java
@RestController
public class LoginController {

    @RequestMapping(value = "/loginsuccess",produces = "text/plain;charset=UTF-8")
    public String logisuccess(){
        return "登录成功";
    }

    /**
     * 测试资源1 
     * @return
     */
    @GetMapping(value = "/r/r1",produces = {"text/plain;charset=UTF-8"})
    public String r1(){
        return " 访问资源1";
    }
    /**
     * 测试资源2 
     * @return
     */
    @GetMapping(value = "/r/r2",produces = {"text/plain;charset=UTF-8"})
    public String r2(){
        return " 访问资源2";
    }

}
```

