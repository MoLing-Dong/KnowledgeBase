# Java 期末考试重点

## 修饰符

| 修饰符    | 说明     |
| --------- | -------- |
| public    | 公共的   |
| protected | 受保护的 |
| private   | 私有的   |
| default   | 默认的   |

## 控制台编译和运行

```shell
javac -encoding utf-8 Test.java
java Test
```

## 构造器

```java
public class Person {
String name;
int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("Person类的第一个构造方法被调用");
    }

    public Person(int age, String name) {
        this.name = name;
        this.age = age;
        System.out.println("Person类的第二个构造方法被调用");
    }

    public static void main(String[] args) {
        Person p1 = new Person("张三", 20);
        Person p2 = new Person(20, "张三");

    }

}
```

当有两个构造器时，可以通过传入的参数类型来区分调用哪个构造器，但是不能通过参数的顺序来区分，因为参数的顺序不同，但是类型相同，编译器无法区分。

## 重载(Overload)

### 什么是重载

重载是指方法名相同，但是参数列表不同的方法，重载的方法必须在同一个类中，重载的方法可以有不同的返回值类型。

```java
    public class Person {
    String name;
    int age;
    // 重载
    public int add(int a, int b) {
        return a + b;
    }
    public int add(int a, int b, int c) {
        return a + b + c;

    }
    }
```

当有两个方法名相同，但是参数列表不同的方法时，称为重载，重载的方法必须在同一个类中，重载的方法可以有不同的返回值类型。

## 重写(覆盖|Override)

```java

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public static void main(String[] args) {
        Person p1 = new Person("张三", 20);
        Person p2 = new Person(20, "张三");
        System.out.println(p1);
        System.out.println(p2);
    }
```

对于 Object 类中的 toString 方法，我们可以重写，这样在输出对象时，就会输出我们重写的内容，而不是默认的对象地址。

## 类变量

加上 static 修饰符的变量，称为类变量，也叫静态变量，类变量在类加载时就会被初始化，而且只会被初始化一次，类变量不属于任何一个对象，而是属于类，所以类变量也可以称为静态变量。

如果不加 static 修饰符的变量，称为实例变量，也叫成员变量，实例变量在创建对象时才会被初始化，每创建一个对象，就会初始化一次，实例变量属于对象，所以实例变量也可以称为非静态变量。

## 类方法

### 什么是类方法

类方法是静态方法，前面需要有 static 修饰符修饰。类方法内不能涉及有关变量的内容

> 1、不能调用类的对象方法  
> 2、不能引用对象变量  
> 3、类方法不能被重写（覆盖）  
> 4、类方法不能使用 super, this 关键字

<!--  -->

## 抽象

### 抽象类

抽象类是用 abstract 修饰的类，抽象类不能被实例化，抽象类中可以有抽象方法，也可以有非抽象方法，抽象类中的抽象方法必须被子类重写，抽象类中的非抽象方法可以被子类继承，也可以被子类重写。

### 抽象方法

#### 抽象方法是什么

抽象方法是用 abstract 修饰的方法，抽象方法没有方法体，抽象方法必须在抽象类中，抽象方法必须被子类重写。

#### 为什么要有抽象方法

抽象方法是为了让子类重写，子类重写抽象方法，就可以实现多态。

```java

public abstract class Animal {
    public abstract void eat();
    public void sleep() {
        System.out.println("睡觉");
    }
}

public class Cat extends Animal {
    @Override
    public void eat() {
        System.out.println("吃鱼");
    }
}
```

## 接口

### 什么是接口

接口是用 interface 修饰的，接口中的方法都是抽象方法，接口中的变量都是常量，接口中的方法和变量都是 public 的，接口中的方法不能有方法体，接口中的方法必须被实现类重写，接口中的变量必须被实现类初始化。

### 接口和抽象类的区别

1、接口中的方法都是抽象方法，而抽象类中的方法可以是抽象方法，也可以是非抽象方法。



## 编程题

累加器从 1 累加到 100，累加器从 1 累加到 100 的倒数

```java
public class UserAccumulator {
    public static void main(String[] args) {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            sum += i;
        }
        System.out.println(sum);
        double sumA = 0;
        for (int i = 1; i <= 100; i++) {
            sumA += (double) 1 / i;
        }
        System.out.println(sumA);

    }
}
```

### 线程

一共有 1000 张票，有 3 个窗口同时卖票，每张票卖 100 遍，每次卖票需要 1 秒钟，每个窗口卖票的过程中都会打印出当前窗口卖的是第几张票，第几遍卖的，以及剩余票数，要求使用多线程的方式模拟卖票的过程。

## 值传递和引用传递

### 什么是值传递和引用传递

值传递是指将实际参数的值复制一份传递给形式参数，这样在方法内部修改形式参数的值，不会影响实际参数的值。

引用传递是指将实际参数的地址传递给形式参数，这样在方法内部修改形式参数的值，会影响实际参数的值。

```java
public class Test {
    public static void main(String[] args) {
        int a = 10;
        int b = 20;
        swap(a, b);
        System.out.println("a = " + a);
        System.out.println("b = " + b);
    }

    public static void swap(int a, int b) {
        int temp = a;
        a = b;
        b = temp;
    }
}
```
