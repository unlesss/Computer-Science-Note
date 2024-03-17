# 传统Java开发缺陷

![[截屏2023-07-26 11.23.41.png]]
	![[截屏2023-07-26 11.49.18.png]]![[截屏2023-07-26 11.49.37.png]]![[截屏2023-07-26 11.49.59.png]]
## 整合后的Web开发结构

![[截屏2023-07-26 11.50.04.png]]

# Spring 开发缺陷
	EJB 臃肿
	配置文件过多

## Spring 处理模式

![[截屏2023-07-26 13.33.00.png]]


# Spring Boot简介

	对比Spring，简化运行模式

![[截屏2023-07-26 13.40.53.png]]
## 配置结构

![[截屏2023-07-26 13.42.10.png]]
### 微服务结构
	Spring Boot为了满足微服务设计

![[截屏2023-07-26 13.44.11.png]]
	微服务可以减轻单例服务器的负载压力

## Spring Boot框架特点

![[截屏2023-07-26 13.54.24.png]]

# Spring 项目搭建
	使用Gradle进行构建
 
## 导入依赖
	有时存在依赖解析问题
	重复多次建立项目，注入依赖解析成功

## 建立启动程序
```java
@Controller
@EnableAutoConfiguration
public class FirstApplication {
    @RequestMapping("/")
    @ResponseBody
    public String home() {
        return "why";
    }

    public static void main(String[] args) {
        SpringApplication.run(FirstApplication.class, args);
    }

}
```
### 启动信息解析
![[截屏2023-07-27 15.19.38.png]]


