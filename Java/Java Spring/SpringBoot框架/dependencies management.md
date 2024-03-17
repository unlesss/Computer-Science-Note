	![[截屏2023-07-28 09.40.11.png]]

# 插件配置

## 引入公共版本变量

![[截屏2023-07-28 09.40.19.png]]

## 引入插件
	implementation 'io.spring.dependency-management:io.spring.dependency-management.gradle.plugin:1.1.2'
	顶部固定所有版本号

![[截屏2023-07-28 09.54.12.png]]
**可以使用star.spring.io ** 进行项目的构建
下载后直接获取项目压缩文件

### 修改插件引入形式
	SpirngBootPlugin 

bootRun任务：使当前项目以Spring Boot方式运行，可以通过命令行模式进行运行

![[截屏2023-08-18 13.57.31.png|650]]

![[截屏2023-07-28 10.06.07.png]]

版本号统一交给插件进行管理，但是统一关联不一定都是优点

