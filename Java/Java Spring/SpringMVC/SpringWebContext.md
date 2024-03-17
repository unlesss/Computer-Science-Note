	初始化Web应用上下文

# Framework

## initWebApplicationContext

```java
protected WebApplicationContext initWebApplicationContext() {
        WebApplicationContext rootContext = WebApplicationContextUtils.getWebApplicationContext(this.getServletContext());
        WebApplicationContext wac = null;
        if (this.webApplicationContext != null) {
            wac = this.webApplicationContext;
            if (wac instanceof ConfigurableWebApplicationContext) {
                ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext)wac;
                if (!cwac.isActive()) {
                    if (cwac.getParent() == null) {
                        cwac.setParent(rootContext);//父容器
                    }

                    this.configureAndRefreshWebApplicationContext(cwac);
                }
            }
        }

        if (wac == null) {
            wac = this.findWebApplicationContext();
        }

        if (wac == null) {
            wac = this.createWebApplicationContext(rootContext);
        }

        if (!this.refreshEventReceived) {
            synchronized(this.onRefreshMonitor) {
                this.onRefresh(wac);
            }
        }

        if (this.publishContext) {
            String attrName = this.getServletContextAttributeName();
            this.getServletContext().setAttribute(attrName, wac);
        }

        return wac;
    }

```
![[截屏2023-08-08 13.50.56 3.png]]

## OnRefresh
	在子类中实现

### DispatcherServlet
	实现

![[截屏2023-08-08 13.53.25.png]]
#### 初始化策略

![[截屏2023-08-08 13.54.51.png]]


## initHanderMappings

![[截屏2023-08-08 14.23.42.png]]


![[截屏2023-08-08 14.17.27.png]]
![[截屏2023-08-08 14.15.08.png]]

![[截屏2023-08-08 14.18.03.png]]![[截屏2023-08-08 14.19.39.png]]![[截屏2023-08-08 14.19.54.png]] ![[截屏2023-08-08 14.21.05.png]]

![[截屏2023-08-08 14.28.29.png]]
![[截屏2023-08-08 14.31.38.png]]
![[截屏2023-08-08 14.39.45.png]] ![[截屏2023-08-08 14.40.38.png]]

## HandlerAdapter适配

	围绕其定义进行操作

## 操作过程

![[截屏2023-08-08 14.51.28.png]]

### 继承结构

![[截屏2023-08-08 14.52.18.png]]

#### 初始化方法定义
	p33

![[截屏2023-08-08 14.53.25.png]]
	三项集合
![[截屏2023-08-08 14.57.50.png]]

![[截屏2023-08-08 15.03.04.png]]
![[截屏2023-08-08 15.04.10.png]]

# doService() 请求转发

![[截屏2023-08-08 15.22.58.png]]

![[截屏2023-08-08 15.37.57.png]]
![[截屏2023-08-08 15.53.25.png]] ![[截屏2023-08-08 15.56.45.png]]

# doDispatcher()方法
	源代码解析

## render()方法

### requestDispatcher()实现
