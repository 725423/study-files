# java基础面试题

### **重写和重载**

​	方法的重写 (Overriding)和重载 (Overloading)是java多态性的不同表现，**重写是父类与子类之间多态性的一种表现，重载可以理解成多态的具体表现形式**。 

​	重载： 发生在同一个类中，方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同，发生在编译时

​	重写： 发生在父子类中，方法名、参数列表必须相同，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类；如果父类方法访问修饰符为 private 则子类就不能重写该方法



### **抽象类和接口的比较**

- 从设计层面上看，抽象类提供了一种 IS-A 关系，需要满足里式替换原则，即子类对象必须能够替换掉所有父类对象。而接口更像是一种 LIKE-A 关系，它只是提供一种方法实现契约，并不要求接口和实现接口的类具有 IS-A 关系。
- 从使用上来看，一个类可以实现多个接口，但是不能继承多个抽象类。
- 接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。
- 接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限。

### **里氏替换原则**

​	里氏替换原则的内容可以描述为： “派生类（子类）对象可以在程式中代替其基类（超类）对象

### **SOLID (面向对象设计)**

​	在[程序设计](https://baike.baidu.com/item/程序设计)领域，**SOLID**（**单一功能、开闭原则、里氏替换、接口隔离**以及**依赖反转**）是由罗伯特·C·马丁在21世纪早期引入的[记忆术](https://baike.baidu.com/item/记忆术)首字母缩略字，指代了[面向对象编程](https://baike.baidu.com/item/面向对象编程)和[面向对象设计](https://baike.baidu.com/item/面向对象设计)的五个基本原则。当这些原则被一起应用时，它们使得一个[程序员](https://baike.baidu.com/item/程序员)开发一个容易进行[软件维护](https://baike.baidu.com/item/软件维护)和扩展的系统变得更加可能。SOLID所包含的原则是通过引发编程者进行软件[源代码](https://baike.baidu.com/item/源代码)的[代码重构](https://baike.baidu.com/item/代码重构)进行软件的[代码异味](https://baike.baidu.com/item/代码异味)清扫，从而使得软件清晰可读以及可扩展时可以应用的指南。SOLID被典型的应用在[测试驱动开发](https://baike.baidu.com/item/测试驱动开发)上，并且是[敏捷开发](https://baike.baidu.com/item/敏捷开发)以及[自适应软件开发](https://baike.baidu.com/item/自适应软件开发)的基本原则的重要组成部分

### **反射的优点**

- **可扩展性**   ：应用程序可以利用全限定名创建可扩展对象的实例，来使用来自外部的用户自定义类。
- **类浏览器和可视化开发环境**   ：一个类浏览器需要可以枚举类的成员。可视化开发环境（如 IDE）可以从利用反射中可用的类型信息中受益，以帮助程序员编写正确的代码。
- **调试器和测试工具**   ： 调试器需要能够检查一个类里的私有成员。测试工具可以利用反射来自动地调用类里定义的可被发现的 API 定义，以确保一组测试中有较高的代码覆盖率。

### **反射的缺点**

尽管反射非常强大，但也不能滥用。如果一个功能可以不用反射完成，那么最好就不用。在我们使用反射技术时，下面几条内容应该牢记于心。

- **性能开销**   ：反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化。因此，反射操作的效率要比那些非反射操作低得多。我们应该避免在经常被执行的代码或对性能要求很高的程序中使用反射。
- **安全限制**   ：使用反射技术要求程序必须在一个没有安全限制的环境中运行。如果一个程序必须在有安全限制的环境中运行，如 Applet，那么这就是个问题了。
- **内部暴露**   ：由于反射允许代码执行一些在正常情况下不被允许的操作（比如访问私有的属性和方法），所以使用反射可能会导致意料之外的副作用，这可能导致代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。

### **Java 与 C++ 的区别**

- Java 是纯粹的面向对象语言，所有的对象都继承自 java.lang.Object，C++ 为了兼容 C 即支持面向对象也支持面向过程。
- Java 通过虚拟机从而实现跨平台特性，但是 C++ 依赖于特定的平台。
- Java 没有指针，它的引用可以理解为安全指针，而 C++ 具有和 C 一样的指针。
- Java 支持自动垃圾回收，而 C++ 需要手动回收。
- Java 不支持多重继承，只能通过实现多个接口来达到相同目的，而 C++ 支持多重继承。
- Java 不支持操作符重载，虽然可以对两个 String 对象执行加法运算，但是这是语言内置支持的操作，不属于操作符重载，而 C++ 可以。
- Java 的 goto 是保留字，但是不可用，C++ 可以使用 goto

### **谈谈你对面向接口编程的理解**

​	接口体现的是一种规范和实现分离的设计哲学，充分利用接口可以极好地降低程序各模块之间的耦合，从而提高系统的可扩展性和可维护性。基于这种原则，很多软件架构设计理论都倡导“面向接口”编程，而不是面向实现类编程，希望通过面向接口编程来降低程序的耦合

### **java关键字**

​		48个关键字：**abstract**、assert、**boolean**、**break**、**byte**、**case**、**catch**、char、class、**continue**、**default**、do、double、else、enum、extends、final、finally、float、for、if、implements、import、int、interface、instanceof、long、native、new、package、private、protected、public、return、short、static、strictfp、super、switch、**synchronized**、this、throw、throws、**transient**、try、void、volatile、while

​		2个保留字（现在没用以后可能用到作为关键字）：goto、const

​		3个特殊直接量：true、false、null

### java语言的三大特性

​		封装：封装就是把属于同一类事物的共性（包括属性和方法）归到一个类中，以方便使用

​		好处：1.实现了专业的分工。将实现某一特定功能的代码封装成一个独立实体后，程序员可以在需要的时候使用，从而实现了分工。2.隐藏信息，实现细节。通过控制访问权限，可以将不想让客户端程序员看到的信息隐藏起来。

​		继承：个性对共性的属性和方法的接受，并加入个性特有的方法。

​		目的：实现代码的复用

​		注意：父类的私有属性和构造方法并不能被继承

​		多态：以封装和继承为基础，相同的事物，调用其相同的方法，参数也相同，但表现行为却不同

​		理解：子类以父类的身份出现需要向上转型，向上转型是由jvm自动实现的，是安全的，但向下转型是不安全的，需要强制转换，子类以父类的身份出现时，自己的特有属性和方法将不能再使用        **在向下转型之前，它得先向上转型**

