---
title: JAVA设置模式学习笔记
date: 2013-09-01
categories: 软件开发
tags:
	- 学习笔记
	- JAVA
	- 设计模式
---

[TOC]

## 遵循原则
### 单一职责原则：
1、一个类或接口只有一个职责；  
2、应该仅有一个原因引起类的变更；  

在日常使用中很难完全做到。
提高了可读性和可维护性，降低变更引起的风险。

<!--more-->

### 里氏替换原则：
所有引用基类的地方必须能透明地使用其子类对象。  
父类出现的地方子类就可以出现，反之未必。  
避免子类的个性。  

### 依赖倒置原则：
高层模块不应该依赖底层模块，两者都应该依赖其抽象类；  
抽象不应该依赖细节；  
细节应该依赖抽象。  

#### Java中的常见规则：
- 接口负责定义public属性和方法，并且声明与其他对象的依赖关系，抽象类负责公共构造部分的实现，实现类准确的实现业务逻辑，同时在适当的时候对父类进行细化。

- 各个类或模块的实现彼此独立，不相互影响，实现模块间的松耦合。
接口隔离原则

- 接口尽可能小，一个接口只服务于一个子模块或业务逻辑
- 已经被污染的接口，若变更的风险较大，则采用适配器模式进行处理。

### 迪米特法则
核心观念是类间解耦，弱耦合；  
一个类应该对自己需要耦合或者调用者的类知道的最少。  

### 开闭原则
一个软件实体(类、模块、函数)应该对外扩展开放，对修改关闭。  
运维尽量减少对原代码的修改，保持历史代码的纯洁性，提高系统的稳定性；  

## 设计模式
### 单例模式（Singleton Pattern）
定义：确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。
  
使用场景：  
1、要求生成唯一序列号的环境；   
2、整个项目需要一个共享访问点或共享数据。  
3、创建对象需要消耗的资源过多，如访问IO和数据库等资源。  

#### 懒汉模式：

```
publicclass SingletonClass1 {

   private SingletonClass1 instance = new SingletonClass1();

   private SingletonClass1(){}

   public SingletonClass1 getInstance(){

       returninstance;

   }

}
```

#### 饿汉模式：

```
publicclass SingletonClass2 {

   /**静态内部类*/

   privatestaticclass SingletonInstance {

       //因为instance是静态的，不会构造多次

       privatestaticfinal SingletonClass2 instance = new SingletonClass2();

   }

   private SingletonClass2(){}

   public SingletonClass2getInstance(){

       return SingletonInstance.instance;

   }

}
```

问题：一个单例对象在内存中长久不使用，JVM就认为这个对象是垃圾，在CPU资源空闲的的情况下该对象会被清理掉； 通常使用Spring可以让对象长久驻留内存。  


### 工厂方法模式（Factory Pattern）
定义：用于创建对象的接口，让子类决定实例化哪一个类，工厂方法使用一个类的实例化延迟到其子类。  
`示例`:

```
publicclass ConcreteCreator extends Creator{

   /**创建一个产品对象，创建类型可自行设定*/

   @Override

   public <T extends Procuct> T createProduct(Class c) {

       Procuct product = null;

       try {

          product = (Procuct) Class.forName(c.getName()).newInstance();

       } catch (Exception e) {

          e.printStackTrace();

       }

       return (T)product;

   }

}
```

`补充说明`：  
工厂方法，有很多扩展，比如简单的静态工厂模式，升级的多级工厂类，抽象工厂模式。  

### 模板方法模式（Template Method Pattern）
定义：定义一个操作中的算法框架放在抽象类中，而将一些步骤延迟到子类中。使得子类可以不改变算法的结构即可重定义该算法的某些特定步骤。（父类提供公共的模板方法，子类实现基本方法）  

**注意**：*为防止恶意的操作，一般模板方法都加上final关键字，不允许被覆写。*

`使用场景`：  
1、多个子类有公有的方法，并且逻辑基本相同。  
2、重要、复杂的算法，可以把核心算法设计为模板方法，周边的相关细节功能有各子类实现。  
3、重构时，模板方法经常使用。  

`示例`：

```
publicabstractclass AbstractClass {

   //基本方法

   protectedabstractvoid doSomething();

   //基本方法

   protectedabstractvoid doAnything();

   //模板方法

   publicfinalvoid templeteMethod(){

       //公共的算法

       // .................

       //调用基本方法完成相关的逻辑

       doSomething();

       doAnything();

   }

}
```

### 代理模式（Proxy Pattern）
定义：为其他对象提供一种代理以控制对这个对象的访问。  
普通代理：通过代理来访问角色，不能直接访问真实角色；真实角色的初始化放在代理类中。   
强制代理：真实角色初始化后，不能直接使用，必须获取代理类，用代理类调用对应的方法。  
动态代理：在实现阶段不用关心代理谁，在运行阶段才指定代理那个对象。  

`态代理部分示例`：

``` 
/**
 * 动态代理类处理器
 * InvocationHandler是JDK提供的动态代理接口
 */
publicclass GamePlayerProxyHandler implements InvocationHandler{

   //被代理者
   private Object o = null;

   public GamePlayerProxyHandler(Object o){

       this.o = o;

   }

   @Override
   public Object invoke(Object proxy, Method method, Object[]args) throws Throwable {

       return method.invoke(this.o, args);

   }

}

publicclass Client {

   publicstaticvoid main(String[] args) {

       IGamePlayer player = new GamePlayer();

       //动态产生一个代理者
       IGamePlayer playerProxy = (IGamePlayer) Proxy.newProxyInstance(player.getClass().getClassLoader(), newClass[]{IGamePlayer.class}, new GamePlayerProxyHandler(player));

       //用代理者调用
       playerProxy.login("李四", "aaa");
   }
}
```

### 原型模式（Prototype Pattern）
定义：通过拷贝原型创建新的对象，而不是new。（在内存中以二进制流进行拷贝）  
拷贝有深拷贝和浅拷贝之分。  

使用场景：  
1、资源优化场景：类初始化需要消耗非常多的资源。  
2、性能和安全要求的场景：通过new产生对象需要繁琐的数据准备或访问权限。  
3、一个对象多个修改者场景。  

### 命令模式（Command Pattern）
定义：将一个请求封装成一个对象，从而让你使用不同的请求把客户端参数化，对请求排队或记录请求日志，可以提供命令的撤销和恢复功能；高内聚模式。  

通常包含三个角色：Receive角色（执行具体命令），Command命令角色（声明所有命令），Invoker调用者角色

说明：命令模式结合责任链模式，实现命令族解析任务；结合模板方法模式，可以减少Command子类的膨胀问题。

使用场景：只要认为有命令的地方都可用。


### 观察者模式（Observer Pattern）
定义：定义对象间一种一对多的依赖关系，使得每当一个对象改变状态，则所有依赖于它的对象会得到通知并被自动更新。
   
使用场景：  
1、关联行为场景；  
2、事件多级触发场景；  
3、跨系统的消息交换场景，如消息队列的处理机制。  


### 门面模式（Façade pattern）
定义：要求一个子系统的外部与其内部的通信必须通过一个统一的对象接口。门面模式提供一个高层次的接口，使得子系统更易于使用。  

使用场景：  
1、为一个复杂的模块或子系统提供一个供外界访问的接口。  
2、子系统相对独立，外界对子系统的访问只要黑箱操作即可。  

注意事项：  
1、一个子系统可以有多个门面。  
2、门面不参与子系统内的业务逻辑。  

### 适配器模式（Adapter Pattern）
定义：将一个类的接口变成客户端所期待的另一种接口，从而使原本不匹配而无法在一起工作的两个类一起工作。通常包含三个角色：Target角色、Adapter源角色、Adapter适配器角色  

使用场景：已经投产的项目修改时，经常使用。


