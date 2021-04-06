```
这里主要介绍一些常见的面试题以及分析考点、答案等。
```
### 基础概念

一些基础java知识，各种基础概念等。

#### 访问修饰符有哪些？区别？

||||||
|-|-|-|-|-|
|修饰符|当前类|同包|子类|其他包|
|public|√|√|√|√|
|protected|√|√|√|×|
|default|√|√|×|×|
|private|√|×|×|×|


#### String 是最基本的数据类型吗？

```text
不是。
    Java中的基本数据类型只有8个：
    byte、short、int、long    
    float、double、char、boolean
```

#### short s1 = 1; s1 = s1 + 1;有错吗?short s1 = 1; s1 += 1;有错吗？

```text
对于short s1 = 1; s1 = s1 + 1;
  由于1是int类型，因此s1+1运算结果也是int 型，需要强制转换类型才能赋值给short型。

而short s1 = 1; s1 += 1;
  可以正确编译，因为s1+= 1;相当于s1 = (short)(s1 + 1);其中有隐含的强制类型转换。
```

#### Math.round(11.5) 等于多少？Math.round(-11.5)等于多少？

```text
答：Math.round(11.5)的返回值是12，Math.round(-11.5)的返回值是-11。
四舍五入的原理是在参数上加0.5然后向下取整。
```

#### switch 是否能作用在byte 上，是否能作用在long 上，是否能作用在String上？

```text
（1）在Java 5以前，switch(expr)中，expr只能是byte、short、char、int。
（2）从Java 5开始，Java中引入了枚举类型，expr也可以是enum类型，
（3）从Java 7开始，expr还可以是字符串（String）。
**但是长整型（long）在目前所有的版本中都是不可以的。**
```

#### 构造器（constructor）是否可被重写（override）？

```text
答：构造器不能被继承，因此不能被重写，但可以被重载。
```

#### equals 和 hashcode 作用和区别？

```text
**作用：hashCode()和equals()的作用其实是一样的，
目的都是为了在java里面比较两个对象是否相等一致**
区别：
一个是性能，一个是可靠性
因为重写的equals()里一般比较的较为全面和复杂(它会对这个对象内所以成员变量一一进行比较)，
  这样效率很低，而通过hashCode()对比，则只要生成一个hash值就能比较了，效率很高
  因为hashCode()并不是完全可靠，非常有可能的情况是，两个完全不同的对象的hash值却是一样的。

对于需要大量并且快速的对比的话如果都用equals()去做显然效率太低，所以解决方案为：
    1. 每当需要比较的时候，首先用hashCode（）去对比，而如果hashCode()不一样，则两个对象肯定不一样，
    此时就没有必要再用equals()比较了；
    2. 如果hashCode()相同，则这两个对象有可能相同，这时候再去比较这两个对象的equals()，
    如若equals()也相同，则表示这两个真的相同的，这样既大大提高了效率，又保证了准确性。
```

#### equals 和 == 的区别？

```text
（1）、基础类型比较
    使用==比较值是否相等。
（2）、引用类型比较
    ①重写了equals方法，比如String。
        第一种情况：使用==比较的是String的引用是否指向了同一块内存
        第二种情况：使用equals比较的是String的引用的对象内容是否相等。
    ②没有重写equals方法，比如User等自定义类
    ==和equals比较的都是引用是否指向了同一块内存
例1：
string x = "string";
string y = "string";
string z = new String("string");
system.out.printIn(x==y); // true
System.out.println(x==z);// false
system.out.printIn(x.equals(y)); // true
System.out.printIn(x.equals(z)); // true
代码解读:
  因为x和y 指向的是同一个引用，所以==也是true，
  而newString()方法则重写开辟了内存空间，所以==结果为false，
  而equals比较的一直是值，所以结果都为true.

equals本质上就是==，只不过String和 Integer等重写了equals方法，把它变成了值比较。

```

#### String和StringBuilder、StringBuffer的区别？

```text
String是只读字符串，也就意味着String引用的字符串内容是不能被改变的。
StringBuilder是Java 5中引入的，它和StringBuffer的方法完全相同，
区别在于它是在单线程环境下使用的，因为它的所有方面都没有被synchronized修饰，
因此它的效率也比StringBuffer要高。

字符串的+操作其本质是创建了StringBuilder对象进行append操作，
然后将拼接后的StringBuilder对象用toString方法处理成String对象，
这一点可以用javap -c Test.class命令获得class文件对应的JVM字节码指令就可以看出。
```

#### 抽象的（abstract）方法是否可同时是静态的（static）,是否可同时是本地方法（native），是否可同时被synchronized修饰？

```text
都不能。
    抽象方法需要子类重写，而静态的方法是无法被重写的，因此二者是矛盾的。
    本地方法是由本地代码（如C代码）实现的方法，而抽象方法是没有实现的，也是矛盾的。
    synchronized和方法的实现细节有关，抽象方法不涉及实现细节，因此也是相互矛盾的。
```

#### finally代码块和finalize()方法有什么区别？



### 异常

#### 说说Java中异常的分类

```text
Throwable -> Error,Exception
Error:严重问题，例如内存溢出
Exception ->运行时异常： RuntimeException，编译时异常
```

#### Java中Exception和Error有什么区别？

```Java
同：
  两者都继承于 Throwable 类
异：
  Exception 是java程序运行中可预料的异常情况，分为运行时异常和受检查的异常。Error类一般是指与
  虚拟机相关的问题，如系统崩溃、虚拟机错误，内存空间不足等导致的应用称程序中断。

```





### 面向对象

#### 面向对象的特征有哪些？

```text
1. 抽象：将一类对象的共同特征总结出来构造类的过程，包括数据抽象和行为抽象两方面
2. 继承：继承是从已有类得到继承信息创建新类的过程。提供继承信息的类被称为父类（超类、基类）；
  得到继承信息的类被称为子类（派生类）。
3. 封装：通常认为封装是把数据和操作数据的方法绑定起来，对数据的访问只能通过已定义的接口。
4. 多态：多态性是指允许不同子类型的对象对同一消息作出不同的响应。
  简单的说就是用同样的对象引用调用同样的方法但是做了不同的事情。
```

#### 重载（Overload）和重写（Override）的区别。重载的方法能否根据返回类型进行区分？

```text
方法的重载和重写都是实现多态的方式，
  区别在于前者实现的是编译时的多态性，而后者实现的是运行时的多态性。

重载发生在一个类中，同名的方法如果有不同的参数列表
  （参数类型不同、参数个数不同或者二者都不同）则视为重载；重载对返回类型没有特殊的要求。
重写发生在子父类之间，重写要求有相同的返回类型，
  比父类被重写方法更好访问，不能比父类被重写方法声明更多的异常（里氏代换原则）。


```

#### 说说面向对象的”六原则一法则？

```Java
单一职责原则
　　一个类只做它该做的事情。
开闭原则
　　一个软件实体应当对扩展开放，对修改关闭。即软件实体应尽量在不修改原有代码的情况下进行扩展。
依赖倒转原则
　　程序要依赖于抽象接口，不依赖于具体实现。
里氏替换原则
　　任何时候都可以用子类型替换掉父类型。
接口隔原则
　　接口要小而专，绝不能大而全。
合成聚合复用原则
　　优先使用聚合或合成关系复用代码。
迪米特法则
 　　一个对象应该对其他的对象有尽可能少的了解，又叫最少知识原则。

```

#### 如何实现对象克隆？

```text
 1). 实现Cloneable接口并重写Object类中的clone()方法；
 2). 实现Serializable接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深度克隆
```

