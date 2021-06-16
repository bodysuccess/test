---
title: shiro
tags: spring
categories: 框架
abbrlink: 1c025fc
date: 2021-05-26 20:54:09
---

#### shiro的核心组件

1. `Subject`即主体，外部应用与subject进行交互，subject记录了当前的操作用户，将用户的概念理解为当前操作的主体。外部程序通过subject进行认证授权，而subject是通过SecurityManager安全管理器进行认证授权。
2. SecurityManager即安全管理器，对全部的subject进行安全管理，它是shiro的核心，负责对所有的subject进行安全管理。通过SecurityManager可以完成subject的认证、授权等，实质上SecurityManager是通过Authenticator进行认证，通过Authorizer进行授权，通过SessionManager进行会话管理等
3. `Authenticator`即认证器，对用户身份进行认证，Authenticator是一个接口，shiro提供ModularRealmAuthenticator实现类，通过ModularRealmAuthenticator基本上可以满足大多数需求，也可以自定义认证器
4. `Authorizer`即授权器，用户通过认证器认证通过，在访问功能时需要通过授权器判断用户是否有此功能的操作权限
5. `Realm`即领域，相当于datasource数据源，securityManager进行安全认证需要通过Realm获取用户权限数据，比如：如果用户身份数据在数据库那么realm就需要从数据库获取用户身份信息
6. `sessionManager`即会话管理，shiro框架定义了一套会话管理，它不依赖web容器的session，所以shiro可以使用在非web应用上，也可以将分布式应用的会话集中在一点管理，此特性可使它实现单点登录
7. `CacheManager`即缓存管理，将用户权限数据存储在缓存，这样可以提高性能
8. CredentialsMatcher即密码管理，shiro提供了一套加密/解密的组件，方便开发。比如提供常用的散列、加/解密等功能。

#### shiro配置的核心代码

~~~java
//自定义的Realm 
public class CustomRealm extends AuthorizingRealm {
   //授权方法
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        SimpleAuthorizationInfo info=new SimpleAuthorizationInfo();
        return info;
    }
	//认证方法
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        SimpleAuthenticationInfo info=new SimpleAuthenticationInfo("","密码","");
        return info;
    }
}
//实现自定义的Realm
@Bean
public CustomRealm customRealm(){
    CustomRealm customRealm=new CustomRealm();
    return customRealm;
}
//securityManager安全管理器
@Bean
public SecurityManager securityManager(){
    DefaultWebSecurityManager defaultWebSecurityManager=new DefaultWebSecurityManager();
    defaultWebSecurityManager.setRealm(customRealm());
    return defaultWebSecurityManager;
}
//配置过滤器，过滤或拦截请求
@Bean
public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager){
    ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
    shiroFilterFactoryBean.setSecurityManager(securityManager);
    return shiroFilterFactoryBean;
}
~~~

#### 总结

shiro的Realm负责认证和授权功能，认证成功之后统一交给SecurityManager进行管理，ShiroFilterFactoryBean则负责总工程，对一些必要的数据进行过滤。

subject是认证成功之后的主体，可以通过下面代码获取用户信息

```
Subject subject = SecurityUtils.getSubject();
UsernamePasswordToken token = (UsernamePasswordToken) subject.getPrincipal();
```

sessionManager是shiro的session，用来存储密码认证成功之后的数据

cacheManager是缓存管理，如果我们自定义缓存可实现此接口，用途是提高性能、扩展性。

CredentialsMatcher是密码加密策略。

