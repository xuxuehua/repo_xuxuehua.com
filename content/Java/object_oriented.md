---

title: "object_oriented"
date: 2019-10-10 07:16
collection: oop
---
[TOC]



# 面向对象

面向对象编程，是一种通过对象的方式，把现实世界映射到计算机模型的一种编程方法



Java语言本身提供的机制，包括：

- package
- classpath
- jar

以及Java标准库提供的核心类，包括：

- 字符串
- 包装类型
- JavaBean
- 枚举
- 常用工具类



# class

class是一种对象模版，它定义了如何创建实例，因此，class本身就是一种数据类型

### 

## 定义class

```
class Person {
		public String name;
		public int age;
}
```



一个`class`可以包含多个字段（`field`），字段用来描述一个类的特征

```
class Book {
		public String name;
		public String author;
		public String isbn;
		public double price;
}
```



可以用`private`修饰`field`，拒绝外部访问

```
class Person {
		private String name;
		private int age;
}
```



## 实例字段

在一个`class`中定义的字段，我们称之为实例字段。实例字段的特点是，每个实例都有独立的字段，各个实例的同名字段互不影响



## static 静态字段

实例字段在每个实例中都有自己的一个独立“空间”，但是静态字段只有一个共享“空间”，所有实例都会共享该字段

```
class Person {
    public String name;
    public int age;
    // 定义静态字段number:
    public static int number;
}
```



对于静态字段，无论修改哪个实例的静态字段，效果都是一样的：所有实例的静态字段都被修改了，原因是静态字段并不属于实例

虽然实例可以访问静态字段，但是它们指向的其实都是`Person class`的静态字段。所以，所有实例共享一个静态字段。

因此，不推荐用`实例变量.静态字段`去访问静态字段，因为在Java程序中，实例对象并没有静态字段。在代码中，实例对象能访问静态字段只是因为编译器可以根据实例类型自动转换为`类名.静态字段`来访问静态对象。

推荐用类名来访问静态字段。可以把静态字段理解为描述`class`本身的字段（非实例字段）





# 对象

对象的行为(behavior)： 可以对对象施加哪些操作，或可以对对象施加哪些方法

对象的状态(state)： 当施加哪些方法时，对象如何响应？必须通过调用方法是实现

对象的标识(identity)： 如何辨别具有相同行为与状态的不同对象





# method 方法

操作数据的过程称为方法

需要使用方法（`method`）来让外部代码可以间接修改`field`

```
public class Main {
    public static void main(String[] args) {
        Person rick = new Person();
        rick.setName("Rick");
        rick.setAge(18);
        System.out.println(rick.getName() + ", " + rick.getAge());
    }
}

class Person {
    private String name; //保证只有自身方法能够访问这些实例域
    private int age;

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return this.age;
    }

    public void setAge(int age) {
        if (age < 0 || age > 100) {
            throw new IllegalArgumentException("invalid age value");
        }
        this.age = age;

    }
}

>>>
Rick, 18
```

> 外部代码可以调用方法`setName()`和`setAge()`来间接修改`private`字段



## 定义方法

```
修饰符 方法返回类型 方法名(方法参数列表) {
    若干方法语句;
    return 方法返回值;
}
```





## private方法

有`public`方法，自然就有`private`方法。和`private`字段一样，`private`方法不允许外部调用

定义`private`方法的理由是内部方法是可以调用`private`方法的

```
public class Main {
    public static void main(String[] args) {
        Person rick = new Person();
        rick.setBirth(1989);
        System.out.println(rick.getAge());
    }
}

class Person {
    private String name;
    private int birth;

    public void setBirth(int birth) {
        this.birth = birth;
    }

    public int getAge() {
        return calcAge(2019);
    }

    private int calcAge(int currentYear) {
        return currentYear - this.birth;
    }
}

>>>
30
```

> `calcAge()`是一个`private`方法，外部代码无法调用，但是，内部方法`getAge()`可以调用它
>
> 这个`Person`类只定义了`birth`字段，没有定义`age`字段，获取`age`时，通过方法`getAge()`返回的是一个实时计算的值，并非存储在某个字段的值。这说明方法可以封装一个类的对外接口，调用方不需要知道也不关心`Person`实例在内部到底有没有`age`字段



## 静态方法 

有静态字段，就有静态方法。用`static`修饰的方法称为静态方法。

因为静态方法属于`class`而不属于实例，因此，静态方法内部，无法访问`this`变量，也无法访问实例字段，它只能访问静态字段。

通过实例变量也可以调用静态方法，但这只是编译器自动帮我们把实例改写成类名而已。

通常情况下，通过实例变量访问静态字段和静态方法，会得到一个编译警告。

静态方法经常用于工具类。例如：

- Arrays.sort()
- Math.random()

静态方法也经常用于辅助方法。注意到Java程序的入口`main()`也是静态方法。



调用实例方法必须通过一个实例变量，而调用静态方法则不需要实例变量，通过类名就可以调用。静态方法类似其它编程语言的函数。

```
public class Main {
    public static void main(String[] args) {
        Person.setNumber(99);
        System.out.println(Person.number);
    }
}

class Person {
    public static int number;

    public static void setNumber(int value) {
        number = value;
    }
}

>>>
99
```







## this变量

在方法内部，可以使用一个隐含的变量`this`，它始终指向当前实例。因此，通过`this.field`就可以访问当前实例的字段

如果没有命名冲突，可以省略`this`

```
class Person {
    private String name;

    public String getName() {
        return name; // 相当于this.name
    }
}
```

但是，如果有局部变量和字段重名，那么局部变量优先级更高，就必须加上`this`

```
class Person {
    private String name;

    public void setName(String name) {
        this.name = name; // 前面的this不可少，少了就变成局部变量name了
    }
}
```





## 方法参数

方法可以包含0个或任意个参数。方法参数用于接收传递给方法的变量值。调用方法时，必须严格按照参数的定义一一传递

```
class Person {
    ...
    public void setNameAndAge(String name, int age) {
        ...
    }
}

Person rick = new Person();
rick.setNameAndAge("Rick", 18);
```





## 可变参数

可变参数用`类型...`定义，可变参数相当于数组类型：

```
class Group {
    private String[] names;

    public void setNames(String... names) {
        this.names = names;
    }
}
```

上面的`setNames()`就定义了一个可变参数。调用时，可以这么写：

```
Group g = new Group();
g.setNames("Xiao Ming", "Xiao Hong", "Xiao Jun"); // 传入3个String
g.setNames("Xiao Ming", "Xiao Hong"); // 传入2个String
g.setNames("Xiao Ming"); // 传入1个String
g.setNames(); // 传入0个String
```

完全可以把可变参数改写为`String[]`类型：

```
class Group {
    private String[] names;

    public void setNames(String[] names) {
        this.names = names;
    }
}
```

但是，调用方需要自己先构造`String[]`，比较麻烦。例如：

```
Group g = new Group();
g.setNames(new String[] {"Xiao Ming", "Xiao Hong", "Xiao Jun"}); // 传入1个String[]
```

另一个问题是，调用方可以传入`null`：

```
Group g = new Group();
g.setNames(null);
```

而可变参数可以保证无法传入`null`，因为传入0个参数时，接收到的实际值是一个空数组而不是`null`。



## 参数绑定

调用方把参数传递给实例方法时，调用时传递的值会按参数位置一一绑定



基本类型参数的传递，是调用方值的复制。双方各自的后续修改，互不影响

```
public class Main {
    public static void main(String[] args) {
        Person p = new Person();
        int n = 15;
        p.setAge(n);
        System.out.println(p.getAge());
        n = 20;
        System.out.println(p.getAge());
    }
}


class Person {
    private int age;

    public int getAge() {
        return this.age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

>>>
15
15
```

> 修改外部的局部变量`n`，不影响实例`p`的`age`字段，原因是`setAge()`方法获得的参数，复制了`n`的值，因此，`p.age`和局部变量`n`互不影响





## 构造方法

创建实例的时候，实际上是通过构造方法来初始化实例的

由于构造方法是如此特殊，所以构造方法的名称就是类名。构造方法的参数没有限制，在方法内部，也可以编写任意语句。但是，和普通方法相比，构造方法没有返回值（也没有`void`），调用构造方法，必须用`new`操作符。



```
public class Main {
    public static void main(String[] args) {
        Person p = new Person("Xiao Ming", 15);
        System.out.println(p.getName());
        System.out.println(p.getAge());
    }
}

class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() {
        return this.name;
    }

    public int getAge() {
        return this.age;
    }
}

```



### 默认构造方法

如果一个类没有定义构造方法，编译器会自动为我们生成一个默认构造方法，它没有参数，也没有执行语句，类似这样：

```
class Person {
    public Person() {
    }
}
```



如果我们自定义了一个构造方法，那么，编译器就*不再*自动创建默认构造方法

```
public class Main {
    public static void main(String[] args) {
        Person p = new Person(); // 编译错误:找不到这个构造方法
    }
}

class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() {
        return this.name;
    }

    public int getAge() {
        return this.age;
    }
}

```



如果既要能使用带参数的构造方法，又想保留不带参数的构造方法，那么只能把两个构造方法都定义出来

```
public class Main {
    public static void main(String[] args) {
        Person p1 = new Person("Xiao Ming", 15); // 既可以调用带参数的构造方法
        Person p2 = new Person(); // 也可以调用无参数构造方法
    }
}

class Person {
    private String name;
    private int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() {
        return this.name;
    }

    public int getAge() {
        return this.age;
    }
}

```



没有在构造方法中初始化字段时，引用类型的字段默认是`null`，数值类型的字段用默认值，`int`类型默认值是`0`，布尔类型默认值是`false`：

```
class Person {
    private String name; // 默认初始化为null
    private int age; // 默认初始化为0

    public Person() {
    }
}
```

也可以对字段直接进行初始化：

```
class Person {
    private String name = "Unamed";
    private int age = 10;
}
```



既对字段进行初始化，又在构造方法中对字段进行初始化

`new Person("Xiao Ming", 12)`的字段值最终由构造方法的代码确定。

```
class Person {
    private String name = "Unamed";
    private int age = 10;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```



### 多构造方法

可以定义多个构造方法，在通过`new`操作符调用的时候，编译器通过构造方法的参数数量、位置和类型自动区分：

```
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Person(String name) {
        this.name = name;
        this.age = 12;
    }

    public Person() {
    }
}
```

如果调用`new Person("Xiao Ming", 20);`，会自动匹配到构造方法`public Person(String, int)`。

如果调用`new Person("Xiao Ming");`，会自动匹配到构造方法`public Person(String)`。

如果调用`new Person();`，会自动匹配到构造方法`public Person()`。

一个构造方法可以调用其他构造方法，这样做的目的是便于代码复用。调用其他构造方法的语法是`this(…)`：

```
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Person(String name) {
        this(name, 18); // 调用另一个构造方法Person(String, int)
    }

    public Person() {
        this("Unnamed"); // 调用另一个构造方法Person(String)
    }
}
```



## 方法重载

如果有一系列方法，它们的功能都是类似的，只有参数有所不同，那么，可以把这一组方法名做成*同名*方法

这种方法名相同，但各自的参数不同，称为方法重载（`Overload`）

方法重载的返回值类型通常都是相同的

```
class Hello {
		public void hello() {
				System.out.println("Hello, World!");
		}
		
		public void hello(String name) {
				System.out.println("Hello, " + name + "!");
		}
		
		public void hello(String name, int age) {
				if (age < 18) {
						System.out.println("Hi, " + name + "!");
				} else {
						System.out.println("Hello, " + name + "!");
				}
		}
}
```





# 作用域

## public

定义为`public`的`class`、`interface`可以被其他任何类访问：

```
package abc;
public class Hello {
		public void hi() {
		}
}
```



定义为`public`的`field`、`method`可以被其他类访问，前提是首先有访问`class`的权限

如果不确定是否需要`public`，就不声明为`public`，即尽可能少地暴露对外的字段和方法



一个`.java`文件只能包含一个`public`类，但可以包含多个非`public`类。如果有`public`类，文件名必须和`public`类的名字相同





## private

定义为`private`的`field`、`method`无法被其他类访问

`private`访问权限被限定在`class`的内部，而且与方法声明顺序*无关*。推荐把`private`方法放到后面，因为`public`方法定义了类对外提供的功能，阅读代码的时候，应该先关注`public`方法

```
package abc;

public class Hello {
    public void hello() {
        this.hi();
    }
    
    // 不能被其他类调用:
    private void hi() {
    }
}
```



### 嵌套类

由于Java支持嵌套类，如果一个类内部还定义了嵌套类，那么，嵌套类拥有访问`private`的权限

```
public class Main {
    public static void main(String[] args) {
        Inner i = new Inner();
        i.hi();
    }

    // private方法:
    private static void hello() {
        System.out.println("private hello!");
    }

    // 静态内部类:
    static class Inner {
        public void hi() {
            Main.hello();
        }
    }
}

>>>
private hello!
```



## protected

`protected`作用于继承关系。定义为`protected`的字段和方法可以被子类访问，以及子类的子类

```
package abc;

public class Hello {
    // protected方法:
    protected void hi() {
    }
}
```

```
package xyz;

class Main extends Hello {
    void foo() {
        Hello h = new Hello();
        // 可以访问protected方法:
        h.hi();
    }
}
```



## package

包作用域是指一个类允许访问同一个`package`的没有`public`、`private`修饰的`class`，以及没有`public`、`protected`、`private`修饰的字段和方法

```
package abc;

// package权限的类:
class Hello {
    
    // package权限的方法:
    void hi() {
    }
}
```



只要在同一个包，就可以访问`package`权限的`class`、`field`和`method`

```
package abc;

class Main {
    void foo() {
        // 可以访问package权限的类:
        Hello h = new Hello();
        // 可以调用package权限的方法:
        h.hi();
    }
}
```



把方法定义为`package`权限有助于测试，因为测试类和被测试类只要位于同一个`package`，测试代码就可以访问被测试类的`package`权限方法



# 局部变量

在方法内部定义的变量称为局部变量，局部变量作用域从变量声明处开始到对应的块结束。方法参数也是局部变量

```
package abc;

public class Hello {
    void hi(String name) { // ①
        String s = name.toLowerCase(); // ②
        int len = s.length(); // ③
        if (len < 10) { // ④
            int p = 10 - len; // ⑤
            for (int i=0; i<10; i++) { // ⑥
                System.out.println(); // ⑦
            } // ⑧
        } // ⑨
    } // ⑩
}
```

> - 方法参数name是局部变量，它的作用域是整个方法，即①～⑩；
> - 变量s的作用域是定义处到方法结束，即②～⑩；
> - 变量len的作用域是定义处到方法结束，即③～⑩；
> - 变量p的作用域是定义处到if块结束，即⑤～⑨；
> - 变量i的作用域是for循环，即⑥～⑧。



# final 修饰符

Java还提供了一个`final`修饰符。`final`与访问权限不冲突，可以修饰`class`、`field`和`method`



## 阻止被继承

用`final`修饰`class`可以阻止被继承

```
package abc;

// 无法被继承:
public final class Hello {
    private int n = 0;
    protected void hi(int t) {
        long i = t;
    }
}
```



## 阻止被子类覆写

用`final`修饰`method`可以阻止被子类覆写

```
package abc;

public class Hello {
    // 无法被覆写:
    protected final void hi() {
    }
}
```



## 阻止被重新赋值

用`final`修饰`field`可以阻止被重新赋值

```
package abc;

public class Hello {
    private final int n = 0;
    protected void hi() {
        this.n = 1; // error!
    }
}
```



## 阻止被重新赋值

用`final`修饰局部变量可以阻止被重新赋值

```
package abc;

public class Hello {
    protected void hi(final int t) {
        t = 1; // error!
    }
}
```





# instance

nstance是对象实例，instance是根据class创建的实例，可以创建多个instance，每个instance类型相同，但各自属性可能不相同



## 创建实例

定义了class，只是定义了对象模版，而要根据对象模版创建出真正的对象实例，必须用new操作符。

new操作符可以创建一个实例，然后，我们需要定义一个引用类型的变量来指向这个实例

```
Person rick = new Person();
```



有了指向这个实例的变量，我们就可以通过这个变量来操作实例。访问实例变量可以用`变量.字段`

```
rick.name = "Rick Xu";
rick.age = 18;
System.out.println(rick.name);
```











