	应用扩容，满足流量需求，将流量均衡分配
# RPC 框架

![[Pasted image 20240327163539.png]]

![[Pasted image 20240327163451.png]]
![[Pasted image 20240327163526.png]]

可以解决http通信慢的问题

此分层结构和依赖引用下，**各层级模块不能循环依赖**，同时 lottery-interfaces 作为系统的 war 包工程，在构建工程时候需要依赖于 POM 中配置的相关信息。

# POM配置

作为 Lottery 工程下的主 pom.xml 需要完成对 SpringBoot 父文件的依赖，此外还需要定义一些用于其他模块可以引入的配置信息，比如：jdk版本、编码方式等。而其他层在依赖于工程总 pom.xml 后还需要配置自己的信息。

**在父类模块中一般只提供基础的定义，不提供各个Jar包的引入配置**。如果在父类 POM 中引入了所有的 Jar，那么各个模块无论是否需要这个 Jar 都会被自动引入进去，造成没必要的配置，也会影响对核心Jar的扰乱。

依赖父POM配置parent、构建包类型packaging、需要引入的包dependencies、构建信息build之所以要配置这个是有些时候在这个模块工程下还可能会有一些差异化信息的引入。

lottery-interfaces 是整个程序的出口，也是用于构建 War 包的工程模块，所以你会看到一个 <packaging>war</packaging> 的配置。
在 dependencies 会包含所有需要用到的 SpringBoot 配置，也会包括对其他各个模块的引入。
在 build 构建配置上还会看到一些关于测试包的处理，比如这里包括了资源的引入也可以包括构建时候跳过测试包的配置。

在 SpringBoot 的使用中，你会看到各种 xxx-starter，它们这些组件的包装都是用于完成桥梁的作用，把一些服务交给 SpringBoot 启动时候初始化或者加载配置等操作
