#  Java简介

## Java介于编译型语言和解释型语言之间

Java是将代码编译成一种“字节码”，它类似于抽象的CPU指令，然后，针对不同平台编写虚拟机，不同平台的虚拟机负责加载字节码并执行，这样就实现了“一次编写，到处运行”的效果。
## 三种版本

Java SE就是标准版，包含标准的JVM和标准库，而Java EE是企业版，它只是在Java SE的基础上加上了大量的API和库，以便方便开发Web应用、数据库、消息服务等，Java EE的应用使用的虚拟机和Java SE完全相同。

Java ME就和Java SE不同，它是一个针对嵌入式设备的“瘦身版”，Java SE的标准库无法在Java ME上使用，Java ME的虚拟机也是“瘦身版”。![[截屏2024-03-13 10.25.43.png]]
## JDK JRE JSR JCP

JDK：Java Development Kit
JRE：Java Runtime Environment 运行字节码的虚拟机
![[截屏2024-03-13 10.27.46.png]]
JSR规范：Java Specification Request
JCP组织：Java Community Process 审核JSR

RI：Reference Implementation JSR标准的参考实现
TCK：Technology Compatibility Kit
