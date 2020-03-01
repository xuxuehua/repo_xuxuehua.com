---
title: "polymorphism 多态"
date: 2019-10-15 08:56
collection: oop
---
[TOC]



# 多态

即多种形态的，运行期才能动态决定调用的子类方法。对某个类型调用某个方法，执行的实际方法可能是某个子类的覆写方法

Java的对象变量是多态的，它们能保存不止一种类型的对象，可以保存的是声明类型的对象，或声明类型的子类的对象



## override 

在继承关系中，子类和父类中存在名称和参数完全相同的方法，被称为覆写（Override）

在`Person`类中，我们定义了`run()`方法：

```
class Person {
    public void run() {
        System.out.println("Person.run");
    }
}
```

在子类`Student`中，覆写这个`run()`方法：

```
class Student extends Person {
    @Override
    public void run() {
        System.out.println("Student.run");
    }
}
```



### @Override 错误检测

加上`@Override`可以让编译器帮助检查是否进行了正确的覆写。希望进行覆写，但是不小心写错了方法签名，编译器会报错。

但`@Override`不是必需的

```
public class Main {
    public static void main(String[] args) {
    }
}

class Person {
    public void run() {}
}

public class Student extends Person {
    @Override // Compile error!
    public void run(String s) {}
}

```



Java的实例方法调用是基于运行时的实际类型的动态调用，而非变量的声明类型

```
public class Main {
    public static void main(String[] args) {
        Person p = new Student();
        p.run();
    }
}

class Person {
    public void run() {
        System.out.println("Person.run");
    }
}

class Student extends Person {
    @Override
    public void run() {
        System.out.println("Student.run");
    }
}

>>>
Student.run
```



## overload

Override和Overload不同的是，如果方法签名如果不同，就是Overload，Overload方法是一个新方法；如果方法签名相同，并且返回值也相同，就是`Override`



## 覆写Object方法

因为所有的`class`最终都继承自`Object`，而`Object`定义了几个重要的方法：

- `toString()`：把instance输出为`String`；
- `equals()`：判断两个instance是否逻辑相等；
- `hashCode()`：计算一个instance的哈希值。

在必要的情况下，我们可以覆写`Object`的这几个方法

```
class Person {
    ...
    // 显示更有意义的字符串:
    @Override
    public String toString() {
        return "Person:name=" + name;
    }

    // 比较是否相等:
    @Override
    public boolean equals(Object o) {
        // 当且仅当o为Person类型:
        if (o instanceof Person) {
            Person p = (Person) o;
            // 并且name字段相同时，返回true:
            return this.name.equals(p.name);
        }
        return false;
    }

    // 计算hash:
    @Override
    public int hashCode() {
        return this.name.hashCode();
    }
}
```



## super 调用

在子类的覆写方法中，如果要调用父类的被覆写的方法，可以通过`super`来调用

```
class Person {
    protected String name;
    public String hello() {
        return "Hello, " + name;
    }
}

Student extends Person {
    @Override
    public String hello() {
        // 调用父类的hello()方法:
        return super.hello() + "!";
    }
}
```



## final 禁止覆写

继承可以允许子类覆写父类的方法。如果一个父类不允许子类对它的某个方法进行覆写，可以把该方法标记为`final`。用`final`修饰的方法不能被`Override`

```
class Person {
    protected String name;
    public final String hello() {
        return "Hello, " + name;
    }
}

Student extends Person {
    // compile error: 不允许覆写
    @Override
    public String hello() {
    }
}
```



如果一个类不希望任何其他类继承自它，那么可以把这个类本身标记为`final`。用`final`修饰的类不能被继承：

```
final class Person {
    protected String name;
}

// compile error: 不允许继承自Person
Student extends Person {
}
```



对于一个类的实例字段，同样可以用`final`修饰。用`final`修饰的字段在初始化后不能被修改。

```
class Person {
		public final String name = "Unamed";
}
```



对`final`字段重新赋值会报错：

```
Person p = new Person();
p.name = "New Name"; // compile error!
```



可以在构造方法中初始化final字段：

```
class Person {
		public final String name;
		public Person(String name) {
				this.name = name;
		}
}
```

这种方法更为常用，因为可以保证实例一旦创建，其`final`字段就不可修改





# 抽象类

表达概念而无法构造出实体的类

因为无法执行抽象方法，因此这个类也必须申明为抽象类（abstract class）

使用`abstract`修饰的类就是抽象类 



## abstract 

表达概念而无法实现具体代码的函数，抽象方法用`abstract`修饰

如果父类的方法本身不需要实现任何功能，仅仅是为了定义方法签名，目的是让子类去覆写它，那么，可以把父类的方法声明为抽象方法

```
class Person {
		public abstract void run();
}
```



因为抽象类本身被设计成只能用于被继承，因此，抽象类可以强迫子类实现其定义的抽象方法，否则编译会报错。因此，抽象方法实际上相当于定义了“规范”

```
public class Main {
    public static void main(String[] args) {
        Person p = new Student();
        p.run();
    }
}

abstract class Person {
    public abstract void run();
}

class Student extends Person {
    @Override
    public void run() {
        System.out.println("Student.run");
    }
}

>>>
Student.run
```

> `Person`类定义了抽象方法`run()`，那么，在实现子类`Student`的时候，就必须覆写`run()`方法



## interface 接口

接口是纯抽象类

所有的成员函数都是抽象函数，所有的成员变量都是public static final

在抽象类中，抽象方法本质上是定义接口规范：即规定高层类的接口，从而保证所有子类都有相同的接口实现，这样，多态就能发挥出威力。

```
abstract class Person {
		public abstract void run();
		public abstract String getName();
}
```



如果一个抽象类没有字段，所有方法全部都是抽象方法, 就可以把该抽象类改写为接口：`interface`

interface 就是比抽象类还要抽象的纯抽象接口，因为它连字段都不能有。因为接口定义的所有方法默认都是`public abstract`的，所以这两个修饰符不需要写出来（写不写效果都一样）

```
interface Person {
		void run();
		String getName();
}
```



### implements 实现interface

类似于extends，即接口可以继承接口，但不能继承类，不能实现接口

当一个具体的`class`去实现一个`interface`时，需要使用`implements`关键字

```
class Student implements Person {
    private String name;
    
    public Student(String name) {
        this.name = name;
    }
    
    @Override
    public void run() {
        System.out.println(this.name + " run");
    }
    
    @Override
    public String getName() {
        return this.name;
    }
}
```



### 多接口

在Java中，一个类只能继承自另一个类，不能从多个类继承。但是，一个类可以实现多个`interface`

```
class Student implements Person, Hello { // 实现了两个interface
		...
}
```



### 接口继承

一个`interface`可以继承自另一个`interface`。`interface`继承自`interface`使用`extends`，它相当于扩展了接口的方法

```
interface Hello {
    void hello();
}

interface Person extends Hello {
    void run();
    String getName();
}
```

> `Person`接口继承自`Hello`接口，因此，`Person`接口现在实际上有3个抽象方法签名，其中一个来自继承的`Hello`接口



### default方法

`default`方法的目的是，当我们需要给接口新增一个方法时，会涉及到修改全部子类。如果新增的是`default`方法，那么子类就不必全部修改，只需要在需要覆写的地方去覆写新增方法。

`default`方法和抽象类的普通方法是有所不同的。因为`interface`没有字段，`default`方法无法访问字段，而抽象类的普通方法可以访问实例字段



在接口中，可以定义`default`方法。例如，把`Person`接口的`run()`方法改为`default`方法

```
public class Main {
    public static void main(String[] args) {
        Person p = new Student("Rick Xu");
        p.run();
    }
}

interface Person {
    String getName();
    default void run() {
        System.out.println(getName()+ " run");
    }
}

class Student implements Person {
    private String name;

    public Student(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }
}

>>>
Rick Xu run
```



### 接口的静态字段

因为`interface`是一个纯抽象类，所以它不能定义实例字段。但是，`interface`是可以有静态字段的，并且静态字段必须为`final`类型

```
public interface Person {
    public static final int MALE = 1;
    public static final int FEMALE = 2;
}
```

实际上，因为`interface`的字段只能是`public static final`类型，所以我们可以把这些修饰符都去掉，上述代码可以简写为

```
public interface Person {
    // 编译器会自动加上public statc final:
    int MALE = 1;
    int FEMALE = 2;
}
```

> 编译器会自动把该字段变为`public static final`类型



## 抽象类 vs 接口

Java的接口特指`interface`的定义，表示一个接口类型和一组方法签名，而编程接口泛指接口规范，如方法签名，数据格式，网络协议

|            | abstract class       | interface                   |
| :--------- | :------------------- | --------------------------- |
| 继承       | 只能extends一个class | 可以implements多个interface |
| 字段       | 可以定义实例字段     | 不能定义实例字段            |
| 抽象方法   | 可以定义抽象方法     | 可以定义抽象方法            |
| 非抽象方法 | 可以定义非抽象方法   | 可以定义default方法         |



## 继承关系

合理设计`interface`和`abstract class`的继承关系，可以充分复用代码。一般来说，公共逻辑适合放在`abstract class`中，具体逻辑放到各个子类，而接口层次代表抽象程度。可以参考Java的集合类定义的一组接口、抽象类以及具体子类的继承关系：

```ascii
┌───────────────┐
│   Iterable    │
└───────────────┘
        ▲                ┌───────────────────┐
        │                │      Object       │
┌───────────────┐        └───────────────────┘
│  Collection   │                  ▲
└───────────────┘                  │
        ▲     ▲          ┌───────────────────┐
        │     └──────────│AbstractCollection │
┌───────────────┐        └───────────────────┘
│     List      │                  ▲
└───────────────┘                  │
              ▲          ┌───────────────────┐
              └──────────│   AbstractList    │
                         └───────────────────┘
                                ▲     ▲
                                │     │
                                │     │
                     ┌────────────┐ ┌────────────┐
                     │ ArrayList  │ │ LinkedList │
                     └────────────┘ └────────────┘
```

在使用的时候，实例化的对象永远只能是某个具体的子类，但总是通过接口去引用它，因为接口比抽象类更抽象

```
List list = new ArrayList(); // 用List接口引用具体子类的实例
Collection coll = list; // 向上转型为Collection接口
Iterable it = coll; // 向上转型为Iterable接口
```









# 面向抽象编程

设计程序时先定义接口，再实现类

任何需要再函数间传入传出的一定是接口，而不是具体的类



尽量引用高层类型，避免引用实际子类型的方式，称之为面向抽象编程



## 本质

上层代码只定义规范（例如：`abstract class Person`）；

不需要子类就可以实现业务逻辑（正常编译）；

具体的业务逻辑由不同的子类实现，调用者并不关心。







