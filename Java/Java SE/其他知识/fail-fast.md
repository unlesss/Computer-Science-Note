	并不是Java集合特有的机制，它是一个通用的系统设计思想

# 百科定义

In systems design, a fail-fast system is one which immediately reports at its interface any condition that is likely to indicate a failure. Fail-fast systems are usually designed to stop normal operation rather than attempt to continue a possibly flawed process. Such designs often check the system’s state at several points in an operation, so any failures can be detected early. The responsibility of a fail-fast module is detecting errors, then letting the next-highest level of the system handle them.

	是一种错误检测机制，一旦检测到可能发生错误，就立马抛出异常，程序不继续往下执行。


## eg

	实际操作中快速抛出异常
```java

public UserDomain queryUserById(String userId){
    if(userId==null||"".equals(userId)){
        throw new RuntimeException("error params...");
    }
    //do something
}
```

# 集合中的fail-fast机制


