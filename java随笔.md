# java随笔

#### i++和++i的区别

- i++返回原来的值，++i返回加1后的值（a = i++ ->a = i; i = i + 1.  a = ++i ->i = i + 1, a = i;)

- i++不能作为左值，++i可以

- i++前者是先赋值，后自增；++i后者是先自增，后赋值

- 例如：i = 1；i = i++；此时i还是1；

- ```java
  public static void main(String[] args) {
      int i = 1;
      i = i++;  //i = 1
      int j = i++;  //j = 1
      int k = i + ++i * i++;  // 2 + 3 * 3
      System.out.println("i:" + i);
      System.out.println("j:" + j);
      System.out.println("k:" + k);
  }  // i = 4, j = 1, k = 11
  ```



### 抽象类和接口

![img](https://uploadfiles.nowcoder.com/images/20200822/247263494_1598063769186_4481B830CE7936071BB52924E872A57B)

- 抽象类和接口不能被直接实例化，二者实例化涉及到多态。如果抽象类要实例化，那么抽象类定义的变量必须指向一个子类对象，这个子类继承了抽象类并实现类抽象类的所有抽象方法，如果接口要实例化，那么接口定义的变量要指向一个子类对象，这个子类必须实现这个接口所有的方法
- 抽象类要被子类继承，接口要被子类实现
- 接口里面只能对方法进行声明，抽象类既可以对方法进行声明，也可以对方法进行实现。
- 抽象类里面的抽象方法必须全部被子类实现，如果子类不能全部实现，那么子类也必须是抽象类。接口里面的方法也必须被子类实现，如果子类不能实现，那么子类必须是抽象类
- 抽象类里面可以没有抽象方法
- 如果一个类里面有抽象方法，那么这个类一定是抽象类
- 抽象类中的方法都要被实现，所以抽象方法不能是静态的static，也不能是私有的private
- 接口（类）可以继承一个或多个接口，但类只能继承一个类
- 抽象级别：接口 > 抽象类 > 实现类
- 抽象类主要用来抽象类别，接口主要是用来抽象方法功能。当关注事物的本质的时候，用抽象类。当关注某种操作的时候，用接口。
- 抽象类的功能应该是远多于接口，但是定义抽象类的代价比较高。因为高级英语一个类只能继承一个父类，即在设计抽象类的时候必须要抽象出所有这个类的子类所具有的共同属性和方法；但是类（接口）可以继承多个接口，因此每个接口只需要将特定的动作方法抽象到这个接口即可。也就是说，接口的设计具有更大的可拓展性，而抽象类的设计必须十分谨慎。

- JDK1.7接口只能由常量跟抽象方法，JDK1.8开始可以有默认方法和静态方法，JDK1.9在前面版本的基础上新增了私有方法和私有静态方法。默认方法使用default关键字修饰，需要些方法体来实现具体逻辑。实现类可以不重写默认方法，在需要的时候进行重写。静态方法使用static关键字修饰、定义，同样需要写方法体，实现具体的逻辑，但静态方法不可以被子类实现或继承。默认方法内部可以调用静态方法，但静态方法内部不能调用默认方法，因为静态方法只能调用静态方法。私有方法用private关键字修饰、定义，私有静态方法用private static关键字修饰、定义，private与private static方法只能接口自身内部调用，实现类或子类不可重写/重载，两者都需要写方法体，实现具体逻辑





### 权限修饰符

- **修饰类**：使用public、default（package private，无修饰符）
- **修饰变量和方法**：使用public, private, protected 或 default ( package private，无修饰符)
- 如果一个**类用 public 修饰**，那么它对**任何位置**的所有类可见；如果一个类无修饰符，即 **default** ，那么它只对**同一个包中的其他类可见**，因此也被称为 package private.
- 对于类中的**变量和方法**，当使用 **public 和 无修饰符**时，**访问权限与修饰类时相似**。除此之外，类中的变量和方法，还可以用 private 和 protected 修饰。当使用 **private** 时，该变量或方法只能在**本类中访问**；当使用 **protected** 时，该变量或方法能在**同一个包中**被访问，或者被**不同包的该类的子类访问**。



#### 权限修饰符的设置和运用

- 使用最严格的限制规则，也就是默认使用 private 修饰符。除非某个方法需要暴露给其他类使用，否则一律限制变量和方法仅本类可见；
- 尽量避免使用 public 修饰符，它将会限制你更改代码的灵活性
- 第二点怎么来理解呢？当你使用 public 时，修饰的内容对其他人是可见的，别人可能会使用这些公共的内容，这时候，当你需要更改时，别人也需要随之修改。最主要的是，用的人多了，你根本不知道有谁在用，你还能轻易修改 public 修饰的内容吗，代码的灵活性自然就降低了

 **访问权限控制**

| 修饰符      | 当前类 | 同一个包 | 子类（不同包) | 其他包 |
| ----------- | ------ | -------- | ------------- | ------ |
| `public`    | √      | √        | √             | √      |
| `protected` | √      | √        | √             | ×      |
| no modifier | √      | √        | ×             | ×      |
| `private`   | √      | ×        | ×             | ×      |



### 子类父类关系

- 一个父类可以有多个子类，一个子类只能有一个父类
- **Java类**只支持**单继承**，不支持多继承。也就是说只可以有1个父类
- 但是**接口可以多继承**。接口可以有多个父类



### final,finally,finalize

- final可以修饰**类，变量，方法**，修饰的类不能被继承，修饰的变量不能重新赋值，修饰的方法不能被重写
- finally**用于抛异常**，finally代码块内语句无论是否发生异常，都会在执行finally，常用于一些**流的关闭**
- finalize方法**用于垃圾回收**。一般情况下不需要我们实现finalize，当对象被回收的时候需要释放一些资源，比如socket链接，在对象初始化时创建，整个生命周期内有效，那么需要实现finalize方法，关闭这个链接。但是当调用finalize方法后，并不意味着**GC**会立即回收该对象，所以有可能真正调用的时候，对象又不需要回收了，然后到了真正要回收的时候，因为之前调用过一次，这次又不会调用了，产生问题。所以**不推荐使用finalize方法**