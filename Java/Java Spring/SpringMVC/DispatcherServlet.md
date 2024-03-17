# 继承结构

![[截屏2023-08-08 13.08.53.png]]

## HttpServletBean 
![[截屏2023-08-08 13.15.40.png]]![[截屏2023-08-08 13.23.42.png]]

## FrameworkServlet
![[截屏2023-08-08 13.25.13.png]]
	获取Web应用上下文
	初始化Servlet环境

**由子类实现**

#### ConfigureAndRefreshWebApplicationContext

### doService

#### doDispatcher
	真正的处理逻辑

### service方法覆写
	整体结构遵从原始开发模式


