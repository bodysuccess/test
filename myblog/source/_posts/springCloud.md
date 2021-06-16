---
title: springCloud
tags: springCloud
categories: 框架
abbrlink: 103896d2
date: 2021-05-17 20:37:34
---

#### 微服务是什么

对于微服务，其实没有太多统一定义，通常而言对于微服务架构来说它只是一个架构风格，就是把所有的应用分成一个个独立的模块，每个独立的模块能运行在自己的进程中并各模块之间相互协助，相互通信去完成一个具体的业务。

#### 微服务的优缺点

> 优点

- 每个微服务都很小，这样能聚焦一个指定的业务功能或业务需求
- 微服务能够被小团队单独开发，这个小团队是2到5人的开发人员组成
- 微服务是松耦合的，是有功能意义的服务，无论是在开发阶段或部署阶段都是独立的
- 微服务能使用不同的语言开发
- 微服务易于被一个开发人员理解，修改和维护，这样小团队能够更关注自己的工作成果。无需通过合作才能体现价值
- 微服务允许你利用融合最新技术
- 微服务只是业务逻辑的代码，不会和HTML,CSS 或其他界面组件混合

> 缺点

- 开发人员要处理系统的复杂性
- 随着服务器的增加，运维的工作压力增大
- 系统部署之间存在依赖关系
- 服务之间通信需要成本
- 数据的一致性问题
- 性能的监控等

#### 什么是SpringCloud

SpringCloud是一种微服务框架，也可以说是一种生态，它把微服务之间所需要解决的问题的组件融合在一起来实现微服务架构通信，数据传输等。

#### SpringBoot和SpringCloud的区别

- Sprinboot专注于开发单体微服务项目
- SpringCloud是关注全局的微服务协调框架，它把Springboot开发的单体微服务项目整合在一起，并为各个服务之间提供通信、注册和发现、断路器、路由等
- Springboot可以离开SpringCloud称为一个单体的服务，但是SpringCloud必须依赖于Springboot开发
- Springboot专注为于个体，SpringCloud专注于服务治理

#### SpringCloud Netflix的组件

##### Eureka服务注册及发现

> Eureka的两大组件Eureka Server、Eureka Client

Eureka Server是提供节点服务的，每个服务启动之后会在Eureka Server中进行注册，服务节点的信息也可通过界面直观的感受

Eureka Client是一个java客户端，用来检测服务是否有效，即每30秒Eureka Client会想Eureka Server发送一个心跳，如果Eureka Server在多个周期内没有接收到心跳，Eureka Server则会把该服务从注册中心移除。默认周期为90s。

> 获取Eureka Serve中的以已注册的信息

~~~Java
@RestController
@RequestMapping("/discover")
public class DiscoverController {

    @Autowired
    private DiscoveryClient client;

    @GetMapping("/get")
    public Object getDiscover(){
        //获取所有的服务
        List<String> list = client.getServices();
        list.forEach(s -> System.out.println(s));
		//获取某个服务的信息
        List<ServiceInstance> provide8081 = client.getInstances("PROVIDE8081");
        provide8081.forEach(serviceInstance -> System.out.println(
                "host:"+serviceInstance.getHost()+"----------"
                +"instance:"+serviceInstance.getInstanceId()+"----------"
                +"port:"+serviceInstance.getPort()+"----------"
                +"uri:"+serviceInstance.getUri()+"----------"
                +"metadata:"+serviceInstance.getMetadata()
        ));
        return this.client;
    }
}
~~~

> Eureka和zookeeper的区别

Eureka利用的是CAP理论中AP原则，即可用性和容错性。会造成注册中心返回的是几分钟之前的信息。

zookeeper是利用CAP理论中的CP原则，即一致性和容错性。如果主服务器master宕机之后，会存在一个选举的过程，次选举过程会造成服务不可用。

两则相比之下，对于大部分业务逻辑来说，高可用性更强于一致性。

##### Ribbon负载均衡工具

ribbon是NetFlix的开源项目，提供负载均衡算法，将各个中间件信息链接在一起，简单的来说就是列出所有的机器，通过某种算法实现负载均衡。

> ribbon和nginx的区别

Nginx 是客户端所有请求统一交给 nginx 进行实现负载均衡请求转发，属于服务器端负载均衡。是一种**集中式**的负载均衡器。即请求由 nginx 服务器端进行转发。

Ribbon 是从 eureka 注册中心服务器端上获取服务注册信息列表，缓存到本地，然后在本地实现轮询负载均衡策略。即在**客户端**实现负载均衡。

Nginx 适合于**服务器端**实现负载均衡 比如 Tomcat

Ribbon 适合与在微服务中 RPC 远程调用实现本地服务负载均衡，比如 Dubbo、SpringCloud 中都是采用本地负载均衡。

> 常用的负载均衡算法    

- 轮询算法                  默认
- 随机算法
- 权重算法

负载均衡算法的实现接口为IRule类，我们可以自定义算法来做负载均衡。

> ribbon和feign的区别

- 启动类使用的注解不同，Ribbon用的是@RibbonClient，Feign用的是@EnableFeignClients。

- 服务的指定位置不同，Ribbon是在@RibbonClient注解上声明，Feign则是在定义抽象方法的接口中使用@FeignClient声明。
- 调用方式不同，Ribbon需要自己构建http请求，模拟http请求然后使用RestTemplate发送给其他服务，步骤相当繁琐。

总的来说feign是面向接口编程更符合开发人员的思想，而ribbon+restTemplate对http进行了封装，形成了一套模板化带调用方式。

##### hystrix服务熔断

> hystrix的简介

Hystrix是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，已提高分布式系统的弹性。

“ 断路器 ” 本身是一种开关装置，当某个服务单元发送故障之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

> hystrix的特性

- **服务熔断：**熔断是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务不可用或者响应实践太长时，会进行服务的降级，**进而熔断该节点微服务的调用，快速返回错误的响应信息**。当检测到该节点微服务调用响应正常后恢复调用链路。在SpringCloud框架里熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定的阈值，缺省是5秒内20次调用失败就会启动熔断机制。熔断机制的注解是`HystrixCommand`。
- **服务降级：**降级的目的是为了解决整体项目的压力，而牺牲掉某一服务模块而采取的措施。
- **服务监控：**可视化页面观察服务的信息

![服务监控.png](https://i.loli.net/2021/05/20/Cc4rno9ZPTxitQL.png)

> 服务熔断和服务降级的区别

- **触发原因不太一样**，服务熔断一般是某个服务（下游服务）故障引起，而服务降级一般是从整体负荷考虑；

- **管理目标的层次不太一样**，熔断其实是一个框架级的处理，每个微服务都需要（无层级之分），而降级一般需要对业务有层级之分（比如降级一般是从最外围服务开始）

##### zuul服务网关（已停用）

- 提供统一服务入口，微服务对前台透明
- 聚合后台服务，节省流量，提升性能
- 安全，过滤，流控等API管理功能
- 提供统一服务出口，解耦

##### SpringCloud gateway服务网关

###### 什么是网关

 网关是整个微服务API请求的入口，负责拦截所有请求，分发到服务上去。可以实现日志拦截、权限控制、解决跨域问题、限流、熔断、负载均衡，隐藏服务端的ip，黑名单与白名单拦截、授权等，常用的网关有zuul(netflix的，但是已经停更了)和spring cloud gateway (springcloudalibaba)。这里主要讲springcloud gateway，springcloud gateway是一个全新的项目,其基于spring5.0 以及springboot2.0和项目Reactor等技术开发的网关,其主要的目的是为微服务架构提供一种简单有效的API路由管理方式.

###### 过滤器和网关的对比

过滤器：对单个服务器的请求进行拦截控制

网关：对所有的服务器的请求进行拦截控制

###### zuul和spring cloud gateway的对比

zuul：是Netflix的，是基于servlet实现的，阻塞式的api即基于servlet实现，不支持长连接。

gateway：是springcloud自己研制的微服务网关，是基于Spring5构建，能够实现响应式非阻塞式的Api，支持长连接

###### 网关与nginx区别 

相同点：都是可以实现对api接口的拦截，负载均衡、反向代理、请求过滤等，可以实现和网关一样的效果。

不同点：Nginx采用C语言编写，Gateway属于Java语言编写的， 能够更好让我们使用java语言来实现对请求的处理。

Nginx 属于服务器端负载均衡器。

Gateway 属于本地负载均衡器。 

###### gateway的组成

路由 : 网关的基本模块，有ID，目标URI，一组断言和一组过滤器组成

断言：就是访问该旅游的访问规则，可以用来匹配来自http请求的任何内容，例如headers或者参数

过滤器：这个就是我们平时说的过滤器，用来过滤一些请求的，gateway有自己默认的过滤器，具体请参考官网，我们也可以自定义过滤器，但是要实现两个接口，ordered和globalfilter



