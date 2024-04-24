需要学习的部分：

```
1. javaguide的java基础部分
2. acwing语法基础课（7的课程看好，常用容器没看，看了多线程与锁）
```

## acwing01. 变量、运算符、表达式、输入与输出

> 




## 重写Object的函数

### 代码实现

```java
package acwing07_class_interface;  
  
import java.util.*;  
import java.util.ArrayList;  
import java.util.List;  
  
public class PersonForOverride implements Comparable<PersonForOverride>, Cloneable {  
    private int age;  
    private String name;  
    private Address address;  
  
    public PersonForOverride (String name, int age) {  
        this.name = name;  
        this.age = age;  
    }  
  
    @Override  
    public String toString () {  
        return "Person{" +  
                "name='" + name + '\'' +  
                ", age=" + age +  
                '}';  
    }  
  
    @Override  
    public boolean equals(Object obj) {  
        if (this == obj) return true;  
        // getClass()返回的是Class的元数据，处于同一个class的话返回的就是唯一的Class对象  
        if (obj == null || getClass() != obj.getClass()) return false;  
        // 由于传进来的是Object类型的，所以还需要强转成Person类型  
        // 即使getClass()确认了两个对象属于同一个类，你仍然需要将Object类型的参数转换为你的类类型（例如Person），这样才能访问这个类的具体属性或方法，进行更深入的比较。  
        PersonForOverride p = (PersonForOverride) obj;  
        // Objects.euqals()即使在双方都是null的情况下，仍然返回true  
        // 而name.equals(p.name)如果name是null会爆NullPointerException，因为不能在null上调用方法  
        return age == p.age && Objects.equals(name, p.name);  
    }  
  
    // 重写hashCode方法是为了确保当两个对象通过equals方法比较为相等时，它们的哈希码也应该相同  
    @Override  
    public int hashCode() {  
        return Objects.hash(name, age);  
    }  
  
    /**  
     * 正数：表示当前对象（this）应该排在比较对象（other）之后。  
     * 零：表示两个对象相等，排序时它们之间的顺序不重要。  
     * 负数：表示当前对象（this）应该排在比较对象（other）之前。  
     * @param other the object to be compared.  
     * @return  
     */  
    @Override  
    public int compareTo(PersonForOverride other) {  
        int nameCompare = this.name.compareTo(other.name);  
        if (nameCompare == 0) {  
            // 如果第一个整数小于、等于或大于第二个整数，它分别返回负整数、零或正整数  
            return Integer.compare(this.age, other.age);  
        }  
        return nameCompare;  
    }  
  
    /**  
     * 当一个类实现了Cloneable接口并且希望提供一个克隆（复制）自身的方法时，它通常会重写Object类中的clone()方法。  
     * Object类是Java中所有类的根类，它提供了一个基本的clone()方法实现，但这个方法默认是protected的，并且它的行为是执行浅拷贝。当你在子类中重写clone()方法  
     * 使用super.clone()调用实际上是在调用Object类中已经实现的clone()方法，以利用其提供的克隆功能。  
     *  
     * 当子类和父类有同名的成员（方法或变量），super可以用来引用父类的成员。  
     * 在子类的构造器中，可以通过super()调用父类的构造方法。如果不显式调用，编译器会自动插入对父类无参构造器的调用。  
     *  
     * 使用super.clone()是为了调用在Object类中实现的原始clone()方法，这个方法负责创建对象的一个浅表复制  
     *  
     * 浅拷贝：对于引用类型字段，浅拷贝仅复制引用而不复制引用指向的对象。因此，原始对象和副本指向同一个引用类型实例。  
     * 修改一个列表中的对象会影响另一个列表中的相同对象。  
     *  
     * @return  
     * @throws CloneNotSupportedException  
     *///    @Override  
//    public Object clone() throws CloneNotSupportedException {  
//        return super.clone();  
//    }  
  
    /**  
     * 深拷贝：为其引用类型字段指向的所有对象创建副本。这意味着原始对象和副本不会共享任何引用类型字段指向的对象。  
     */  
    @Override  
    public Object clone() throws CloneNotSupportedException {  
        PersonForOverride cloned = (PersonForOverride) super.clone();  
        cloned.address = (Address) this.address.clone();  
        return cloned;  
    }  
}  
  
class Main {  
    public static void main(String[] args) {  
        List<PersonForOverride> people = new ArrayList<>();  
        PersonForOverride p1 = new PersonForOverride("chile", 23);  
        PersonForOverride p2 = new PersonForOverride("chile", 23);  
        PersonForOverride p3 = new PersonForOverride("Alice", 30);  
        PersonForOverride p4 = new PersonForOverride("Bob", 25);  
        PersonForOverride p5 = new PersonForOverride("Charlie", 35);  
  
        people.add(p1);  
        people.add(p3);  
        people.add(p4);  
        people.add(p5);  
  
        System.out.println(p1.toString());  
        System.out.println(p1.equals(p2));  
  
    }  
}
```

## acwing07. 类与接口

### 7.1 类与对象

类定义一种全新的数据类型，包含一组变量和函数；对象是类这种类型对应的实例。

例如在一间教室中，可以将Student定义成类，表示“学生”这个抽象的概念。那么每个同学就是Student类的一个对象（实例）。

#### 7.1.1 源文件声明规则

- 一个源文件中只能有一个public类。
- 一个源文件可以有多个非public类。
- 源文件的名称应该和public类的类名保持一致。
- 每个源文件中，先写package语句，再写import语句，最后定义类。

#### 7.1.2 类的定义

- public: 所有对象均可以访问
- private: 只有本类内部可以访问
- protected：同一个包或者子类中可以访问
- 不添加修饰符：在同一个包中可以访问
- 静态（带static修饰符）成员变量/函数与普通成员变量/函数的区别
- 所有static成员变量/函数在类中只有一份，被所有类的对象共享；
- 所有普通成员变量/函数在类的每个对象中都有独立的一份；
- 静态函数中只能调用静态函数/变量（公有的函数只能访问所有公有的东西）；普通函数中既可以调用普通函数/变量，也可以调用静态函数/变量。

```java
class Point {
    private int x;
    private int y;


public Point(int x, int y) {
    this.x = x;
    this.y = y;
}

public void setX(int x) {
    this.x = x;
}

public void setY(int y) {
    this.y = y;
}

public int getX() {
    return x;
}

public int getY() {
    return y;
}

public String toString() {
    return String.format("(%d, %d)", x, y);
}
    
    }
```


#### 7.1.3 类的继承

每个类只能继承一个类。

子类和父类中都有toString会优先使用子类中的toString函数

```java
package acwing07_class_interface;  
  
public class ColorPoint extends Point {  
    private String color;  
    public ColorPoint(int x, int y, String color) {  
        // super调用父类的构造函数  
        // 因为直接使用this.x = x，当前类中并没有x，所以无法调用  
        super(x, y);  
        this.color = color;  
    }  
  
    public void setColor(String color) {  
        this.color = color;  
    }  
  
    /**  
     * 子类和父类中都有toString会优先使用子类中的toString函数  
     * @return  
     */  
    public String toString() {  
        return String.format("(%d, %d, %s)", super.getX(), super.getY(), this.color);  
    }  
}
```



#### 7.1.4 类的多态
多态：同一种类型调用同一个函数能有不同的行为

比如都是Point类型但是调用同一个toString函数的结果是不一样的
```java
class main {  
    public static void main(String[] args) {  
        Point point = new Point(2, 3);  
        Point colorPoint = new ColorPoint(2, 3, "blue");  
        System.out.println(point.toString());  
        System.out.println(colorPoint.toString());  
    }  
}
```

输出：
```shell
(2, 3)
(2, 3, blue)
```
### 7.2 接口

interface与class类似。主要用来定义类中所需包含的函数。

接口也可以继承其他接口，一个类可以实现多个接口。

接口时定义一个类中必须要包含哪些函数的工具

#### 7.2.1 接口的定义
接口中不添加修饰符时，默认为public。

一般情况下接口都是public，所以public都可以去掉

```java
interface Role {
    public void greet();
    public void move();
    public int getSpeed();
}
```



#### 7.2.2 接口的继承
每个接口可以继承多个接口

```java
interface Hero extends Role {
    public void attack();
}
```

#### 7.2.3 接口的实现
每个类可以实现多个接口

```java
class Zeus implements Hero {
    private final String name = "Zeus";
    public void attack() {
        System.out.println(name + ": Attack!");
    }

    public void greet() {
        System.out.println(name + ": Hi!");
    }
    
    public void move() {
        System.out.println(name + ": Move!");
    }
    
    public int getSpeed() {
        return 10;
    }

}

```

#### 7.2.4 接口的多态

```java
class Athena implements Hero {
    private final String name = "Athena";
    public void attack() {
        System.out.println(name + ": Attack!!!");
    }

    public void greet() {
        System.out.println(name + ": Hi!!!");
    }
    
    public void move() {
        System.out.println(name + ": Move!!!");
    }
    
    public int getSpeed() {
        return 10;
    }

}

public class Main {
    public static void main(String[] args) {
        Hero[] heros = {new Zeus(), new Athena()};
        for (Hero hero: heros) {
            hero.greet();
        }
    }
}


```








## acwing08. 常用容器（还没看）

### Java原生数组
```java

```
### 8.1 List


```
接口：java.util.List<>

实现：
java.util.ArrayList<>：变长数组
java.util.LinkedList<>：双链表

函数：

add()：在末尾添加一个元素
clear()：清空
size()：返回长度
isEmpty()：是否为空
get(i)：获取第i个元素
set(i, val)：将第i个元素设置为val
```

### 8.2 栈
```
类：java.util.Stack<>

函数：

push()：压入元素
pop()：弹出栈顶元素，并返回栈顶元素
peek()：返回栈顶元素
size()：返回长度
empty()：栈是否为空
clear()：清空

```

### 8.3 队列
```
接口：java.util.Queue<>

实现：

java.util.LinkedList<>：双链表
java.util.PriorityQueue<>：优先队列
默认是小根堆，大根堆写法：new PriorityQueue<>(Collections.reverseOrder())
函数：

add()：在队尾添加元素
remove()：删除并返回队头
isEmpty()：是否为空
size()：返回长度
peek()：返回队头
clear()：清空

```
### 8.4 Set

```
接口：java.util.Set<K>

实现：
- java.util.HashSet<K>：哈希表
- java.util.TreeSet<K>：平衡树

函数：

add()：添加元素
contains()：是否包含某个元素
remove()：删除元素
size()：返回元素数
isEmpty()：是否为空
clear()：清空
java.util.TreeSet多的函数：

ceiling(key)：返回大于等于key的最小元素，不存在则返回null
floor(key)：返回小于等于key的最大元素，不存在则返回null

```

### 8.5 Map

```
接口：java.util.Map<K, V>

实现：

java.util.HashMap<K, V>：哈希表
java.util.TreeMap<K, V>：平衡树
函数：

put(key, value)：添加关键字和其对应的值
get(key)：返回关键字对应的值
containsKey(key)：是否包含关键字
remove(key)：删除关键字
size()：返回元素数
isEmpty()：是否为空
clear()：清空
entrySet()：获取Map中的所有对象的集合
Map.Entry<K, V>：Map中的对象类型
getKey()：获取关键字
getValue()：获取值
java.util.TreeMap<K, V>多的函数：

ceilingEntry(key)：返回大于等于key的最小元素，不存在则返回null
floorEntry(key)：返回小于等于key的最大元素，不存在则返回null
```






## acwing09. 异常处理

## acwing10. 注解与反射


## acwing11. 多线程与锁
### 11.1 多线程

比如图片加载和渲染都是不同的操作，是并行的，不会因为图片加载不出来就整个网页卡死，并且不会浪费cpu

多线程指的是并发
#### 11.1.1 实现多线程
##### 写法1：继承Thread类

```java
class Worker extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 10; i ++ ) {
            System.out.println("Hello! " + this.getName());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Worker worker1 = new Worker();
        Worker worker2 = new Worker();
        worker1.setName("thread-1");
        worker2.setName("thread-2");
        // start会执行run语句
        // 主线程仍然会继续往下走
        worker1.start();
        worker2.start();
    }
}
```

##### 写法2：实现Runnable接口

```java
class Worker1 implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 10; i ++ ) {
            System.out.println("Hello! " + "thread-1");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }
}

class Worker2 implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 10; i ++ ) {
            System.out.println("Hello! " + "thread-2");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }
}

public class Main {
    public static void main(String[] args) {
        new Thread(new Worker1()).start();
        new Thread(new Worker2()).start();
    }
}
```



#### 11.1.2 常用API（针对于Thread）
注意interrupt只能打断sleep()
并不能打断普通的

```
start()：开启一个线程
Thread.sleep(): 休眠一个线程
join()：等待线程执行结束
interrupt()：从休眠中中断线程，抛一个异常

垃圾回收机制就是在所有用户线程结束后结束掉
守护线程：当其他有用线程结束的时候，自动结束当前线程
setDaemon()：将线程设置为守护线程。当只剩下守护线程时，程序自动退出
```

如果将两个子线程都设置成守护线程的话，当主线程结束的时候，两个子线程就自动结束了

### 11.2 锁
lock：获取锁，如果锁已经被其他线程获取，则阻塞
unlock：释放锁，并唤醒被该锁阻塞的其他线程

```java
import java.util.concurrent.locks.ReentrantLock;
class Worker extends Thread {
    public static int cnt = 0;
    private static final ReentrantLock lock = new ReentrantLock();


    @Override
    public void run() {
        for (int i = 0; i < 100000; i ++ ) {
            lock.lock();
            try {
                cnt ++ ;
            } finally {
                lock.unlock();
            }
        }
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Worker worker1 = new Worker();
        Worker worker2 = new Worker();

        worker1.start();
        worker2.start();
        worker1.join();
        worker2.join();

        System.out.println(Worker.cnt);
    }
}
```


### 11.3 同步（Synchronized）
#### 写法1：将Synchronized加到代码块上


```java
class Count {
    public int cnt = 0;
}

class Worker extends Thread {
    public final Count count;

    public Worker(Count count) {
        this.count = count;
    }

    @Override
    public void run() {
        synchronized (count) {
            for (int i = 0; i < 100000; i ++ ) {
                count.cnt ++ ;
            }
        }
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Count count = new Count();

        Worker worker1 = new Worker(count);
        Worker worker2 = new Worker(count);

        worker1.start();
        worker2.start();
        worker1.join();
        worker2.join();

        System.out.println(count.cnt);
    }
}
```

#### 写法2：将Synchronized加到函数上（锁加到了this对象上）

```java
class Worker implements Runnable {
    public static int cnt = 0;

    private synchronized void work() {
        for (int i = 0; i < 100000; i ++ ) {
            cnt ++ ;
        }
    }

    @Override
    public void run() {
        work();
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Worker worker = new Worker();
        Thread worker1 = new Thread(worker);
        Thread worker2 = new Thread(worker);

        worker1.start();
        worker2.start();
        worker1.join();
        worker2.join();

        System.out.println(Worker.cnt);
    }
}
```


#### 11.3.1 wait与notify
```java
package org.yxc;

class Worker extends Thread {
    private final Object object;
    private final boolean needWait;

    public Worker(Object object, boolean needWait) {
        this.object = object;
        this.needWait = needWait;
    }

    @Override
    public void run() {
        synchronized (object) {
            try {
                if (needWait) {
                    object.wait();
                    System.out.println(this.getName() + ": 被唤醒啦！");
                } else {
                    object.notifyAll();
                }
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Object object = new Object();
        for (int i = 0; i < 5; i ++ ) {
            Worker worker = new Worker(object, true);
            worker.setName("thread-" + i);
            worker.start();
        }

        Worker worker = new Worker(object, false);
        worker.setName("thread-" + 5);
        Thread.sleep(1000);
        worker.start();
    }
}


```