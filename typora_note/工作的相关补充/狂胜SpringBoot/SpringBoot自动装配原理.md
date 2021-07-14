# 原理初探

## 自动装配

### pom.xml

- Spring-boot-dependencies : 核心依赖在父工程中
- 我们在写或者引入一些Springboot依赖的时候，不需要指定版本，就是因为有这些版本仓库



启动器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

启动器，就是Springboot的启动场景

比如spring-boot-starter-web, 就会帮我们自动导入web环境所有的依赖！

springboot会将所有的功能场景都变成启动器

我们要用什么功能，就只需要找到对应的启动器就可以了 **starter**   

### 主程序

```java
//本身就是Spring的一个组件
// 程序的入口:标记这是一个springboot应用程序
@SpringBootApplication
public class HelloworldApplication {

    public static void main(String[] args) {
        SpringApplication.run(HelloworldApplication.class, args);
    }
}
```

- - **@SpringBootConfiguration：**    **spingboot的配置**

- - - **@Configuration            spring配置类**

- - - - **@Component           说明这也是一个spring的组件**

- - **@EnableAutoConfiguration：    自动配置**

- - - **@AutoConfigurationPackage      自动导入包**

- - - - **@Import({Registrar.class****})     包注册**

- - - **@Import({AutoConfigurationImportSelector.class})  导入选择器**