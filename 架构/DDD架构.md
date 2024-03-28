	是一种架构设计方法论，而非架构
# DDD（Domain Driven Design）
	领域驱动设计，通过边界划分将复杂业务领域简单化，帮我们设计出清晰的领域和应用边界，可以很容易地实现架构演进。

## 基础概念

### 领域
	即要解决的业务问题域

领域划分，对应更小的问题域或更小的业务范围——**子域** 
聚合根与领域服务负责封装实现业务逻辑。领域服务负责对聚合根进行调度和封装，同时可以对外提供各种形式的服务，对于不能直接通过聚合根完成的业务操作就需要通过领域服务。
### 子域的分类
	核心域、通用域和支撑域。

1. 核心域：决定产品和公司核心竞争力的子域，没有太多个性化的诉求
2. 通用域：同时被多个子域使用的通用功能子域是通用域
3. 支撑域：是必需的，但既不包含决定产品和公司核心竞争力的功能，也不包含通用功能的子域

	通用域则是你需要用到的通用系统，比如认证、权限等等，这类应用很容易买到，没有企业特点限制，不需要做太多的定制化。
	支撑域则具有企业特性，但不具有通用性，例如数据代码类的数据字典等系统。

## 限界上下文
	通过领域的限界上下文，我们就可以在统一的领域边界内用统一的语言进行交流，可以理解为语义环境。

**理论上限界上下文就是微服务的边界。我们将限界上下文内的领域模型映射到微服务，就完成了从问题域到软件的解决方案。**


## 贫血模型和充血模型

### 贫血模型

贫血模型具有一堆属性和set get方法，存在的问题就是通过pojo这个对象上看不出业务有哪些逻辑，一个pojo可能被多个模块调用，只能去上层各种各样的service来调用，这样以后当梳理这个实体有什么业务，只能一层一层去搜service，也就是贫血失忆症，**不够面向对象**

### 充血模型

user用户有改密码，改手机号，修改登录失败次数等操作，都内聚在这个user实体中，每个实体的业务都是清晰的，就是充血模型，充血模型的内存计算会多一些，内聚核心业务逻辑处理。

```java
@NoArgsConstructor
@Getter
public class User extends Aggregate<Long, User> {

    private String userName;

    private String realName;

    private String phone;

    private String password;

    private Date lockEndTime;

    private Integer failNumber;

    private List<Role> roles;

    private Department department;

    private UserStatus userStatus;

    private Address address;

    public User(String userName, String phone, String password) {
        saveUserName(userName);
        savePhone(phone);
        savePassword(password);
    }
    private void saveUserName(String userName) {...}

    private void savePhone(String phone) {...}

    private void savePassword(String password) {...}

    public void saveAddress(String province,String city,String region){...}

    public void saveRole(List<Role> roleList) {...}
}
```

不只是有贫血模型中setter getter方法，还有其他的一些业务方法，这才是面向对象的本质，**通过user实体就能看出有哪些业务存在**。

## 实体值和对象
	领域模型中的领域对象。实体和值对象是组成领域模型的基础单元。



