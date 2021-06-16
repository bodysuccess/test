---
title: swagger总结
tags: swagger
categories: 中间件
abbrlink: 581519cc
date: 2021-04-20 20:57:48
---

#### Swagger简介

###### swagger即是一个接口文档生成工具，也是一个接口调用工具，主要目的是解决前后端的即时交互问题。

###### swagger的配置：

- ApiInfo   文档的简介
- enable    是否启动swagger   以便保证生产环境不启动
- select ().apis ()  可扫描的路径
- select().paths()  可匹配的路径     
- build是固定结束格式

```java
@Configuration
@EnableSwagger2  //开启swagger2
public class SwaggerConfig {
    @Bean
    public Docket docket(Environment environment){
        //读取application.yml读取的环境
        Profiles profiles = Profiles.of("dev","test");
        //判断运行的环境是否是可读取的环境
        boolean flag = environment.acceptsProfiles(profiles);
        
        return  new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(new ApiInfo("小婉Swagger",   //title
                                "当一个闲鱼",			//description
                                "v.1.0",			//版本
                                "47.100.69.105",    //服务器地址
                                new Contact(
                                        "小婉",
                                        "47.100.69.105",
                                        "wlkjava@163.com"
                                ),     				//个人介绍
                                "Apache 2.0",
                                "http://www.apache.org/licenses/LICENSE-2.0",
                                new ArrayList()
                                )
                        )
                //右上角的命名path
                .groupName("learn")
                //设置swagger是否启动
                .enable(flag)
                .select()
                //RequestHandlerSelectors.basePackage扫描包下的所有接口
                //RequestHandlerSelectors.any扫描所有接口    具体的可查看源码
                .apis(RequestHandlerSelectors.basePackage("com.example.swagger.controller"))
                //匹配所有符合的路径
                .paths(PathSelectors.any())
                .build();
    }
}
```

###### swagger的常用注解

- @ApiModel    用在实体类上的注解
- @ApiModelProperty        用在字段上的注解
- @ApiOperation                 用在方法上的注解
- @ApiParam                       用在入参上的注解

```java
@Data
@ApiModel(value = "用户信息表")
public class User {

    @ApiModelProperty(value = "用户名")
    private String userName;
    @ApiModelProperty(value = "密码")
    private String password;
}
```

![image-20210123111636040](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210123111636040.png)

```java
@ApiOperation("测试")
@PostMapping(value = "/test")
public String test(@ApiParam(value ="用户名") String  userName){
    return userName;
}
```





#### 总结：

1、swagger在一些比较难理解的属性或者接口上增加注释信息 （替换了平常书写接口文档的office、wps等）

2、接口文档实时更新（更易于前后端对接、其各部分之间的对接）

3、在线测试工具（替换了接口测试工具，如POSTMan等）