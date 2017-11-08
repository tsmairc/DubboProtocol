# DubboProtocol
描述dubbo调用协议&amp;dubbo注册中心发组&amp;dubbo服务分组

sasl协议
![](https://github.com/tsmairc/DubboProtocol/blob/master/image/sasl.png?raw=true)
SASL是一种用来扩充C/S模式验证能力的机制认证机制,  全称Simple Authentication and Security Layer.<br/>
当你设定sasl时，你必须决定两件事；一是用于交换“标识信 息”（或称身份证书）的验证机制；一是决定标识信息存储方法的验证架构。<br/>
sasl验证机制规范client与server之间的应答过程以及传输内容的编码方法，sasl验证架构决定服务器本身如何存储客户端的身份证书以及如何核验客户端提供的密码。
 

 sasl验证规范规定client跟server应答机制以及传输内容的的编码方式。
 
 下面介绍常用的机制：
 digest-md5
 client和server会共享一个隐性密码，验证过程首先服务器发出 质询，客户端使用此质询与隐性密码计算出一个response返回给服务器，服务计算客户端返回的response跟自己计算的是否一致，因为密码不会传输，不会太容易暴露密码。
 
 
 
 ## dubbo注册中心分组 提供者服务分组 消费者服务分组
 
 1.dubbo.config
 在dubbo配置文件中可以配置多个注册中心，提供者根据id选择不同的注册中心，不填Id则选用默认的注册中心
```xml
<dubbo:registry id="testRegistry" group="testGroup"
		protocol="${dubbo.registry.protocol}" address="${dubbo.registry.address}"
		username="${dubbo.registry.username}" password="${dubbo.registry.password}"
		tokenfile="${dubbo.registry.tokenfile}" check="false" default="true" />
```
上面注册文件中的group是注册中心分组，指接口全在testGroup顶级目录下。default=true表示当前是默认的注册中心，表示当提供者不填注册中心的id，则会默认选中当前的。
![](https://github.com/tsmairc/DubboProtocol/blob/master/image/zookeeper_tree.png?raw=true)

默认的生产者跟消费者配置，主要是分组配置，这里配置上，全部消费者跟生产者没指定服务分组的都会变成这里的默认分组。
```xml
<dubbo:provider timeout="${dubbo.provider.timeout}" group="${dubbo.group}" />

<dubbo:consumer check="false" retries="0" group="${dubbo.group}" />

```

2.提供者
```xml
<dubbo:reference registry="testRegistry" id="testService" interface="com.test.TestService"  group="test"/>
```
上在提供者的group指当前服务的分组，会在当前url加上group这个属性，例如dubbo://127.0.0.1:20881/xxxxxx?group=test

3.消费者
```xml
<dubbo:reference registry="${testRegistry}" id="test" interface="com.test.TestService" group="test"/>
```
上在消费者的group指当前服务的分组，当一个目录存在多个分组的时候，指定了分组消费者才能正确找到想要的提供者url
