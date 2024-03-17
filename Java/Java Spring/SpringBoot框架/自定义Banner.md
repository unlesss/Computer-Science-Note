# 使用文件方法进行自定义

向resource文件夹中加入自定义图案的banner.txt文件

# 使用Banner接口配置

## Banner接口解析

```java
package org.springframework.boot;
import java.io.PrintStream;
import org.springframework.core.env.Environment;

@FunctionalInterface //函数式接口
public interface Banner {
//通过指定的PrintStream 打印流实现启动Banner输出
//profile 项目启动时指派的profile
//sourceClass 应用程序类
//out 实现Banner信息输出
	void printBanner(Environment environment, Class<?> sourceClass, PrintStream out);

	enum Mode {//Banner启动模式
		OFF,//不输出
		CONSOLE,//控制台输出
		LOG//日志中输出
	}
}

```

## Banner接口子类实现

### 定义Banner生成器

#### 代码
**Banner 自定义**

![[截屏2023-07-28 11.41.58.png|640]]

**Banner生成器**

![[截屏2023-07-28 11.41.39.png|640]]

**主程序设置Banner生成**
![[截屏2023-07-28 11.44.27.png|640]]

	setBannerMode(Banner.mode.枚举类型)