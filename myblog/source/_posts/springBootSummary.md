---
title: springBootSummary
tags: spring
categories: 总结
abbrlink: 9cd83123
date: 2021-05-25 20:58:30
---

#### dependencyManagement和dependencies的区别

- dependencyManagement只声明依赖，不实现引入。因此如果子项目需要引入依赖的话需要自己再次声明。目的是为了统一多module之间的version。
- 如果子项目的dependencies实现了具体依赖，可不加版本号直接实现父类即dependencyManagement内声明的版本号
- 如果子项目的dependencies实现了具体依赖且有版本号则以子项目声明的为准。
- 总结类似于java的继承关系。