### 与C++刷题的区别
### 引用对象和传递值
java中基本类型比如int, double, char在java中都是通过值传递的，所以他们传递到方法中的时候是通过副本或者值来传递和引用的
引用类型（比如数组和对象）实际上是通过他们的引用，及内存地址的引用来进行传递的，当在方法中传递一个对象的时候，实际上传递的是对象在内存中的地址。**如果修改这个对象，那么原始对象也会被修改**


### Java语法
```java
// hash
Map<Integer, Integer> hash = new HashMap<Integer, Integer>();
hash.containsKey(x);
hash.get(target - x);
hash.put(i, x);

// hashset
Map<Character> hash = new HashMapK<Character>();
while (hash.contains(c)) {
    hash.remove(s.charAt(j));
    j++;
}
hash.add(c);


// 数组
return new int[] {i, x, 3};
return new int[0]; // 注意这里只能是int[0]后面不用跟括号！
int[] array = new int[256];

// String
int n = s.length();

// 最大最小值
int ans = Math.max(a, b);
long x = Long.MAX_VALUE;
int y = Integer.MAX_VALUE;

```

### 无法`map.put(ch, map.get(ch) ++);`

在Java中，当你试图执行`map.get(ch)++`这样的操作时，会出现编译错误，这是因为`map.get(ch)`返回的是一个`Integer`对象，而不是一个基本数据类型。在Java中，所有的数字字面量，如整数，都被处理成`Integer`对象，而非基本数据类型`int`。`Integer`类是不可变的，这意味着你不能直接修改`Integer`对象的值。

更具体地说，`map.get(ch)++`实际上是尝试进行两个操作：

1. 获取`map`中与键`ch`关联的`Integer`值。
2. 尝试对这个`Integer`对象执行自增（`++`）操作。

第二个操作是不允许的，因为`Integer`对象不支持原地修改（in-place modification）。这种尝试修改会被编译器拒绝，因为`Integer`对象一旦创建，其值就是不可更改的。

正确的做法是使用`map.put()`来更新`map`中的值，如你在代码中已经使用的那样：

`map.put(ch, map.get(ch) + 1);`

这行代码做了以下几件事：

- 通过`map.get(ch)`获取当前的计数。
- 将其值加1。
- 用新的值更新`map`中对应的键。

这种方法确保了你可以正确地更新`HashMap`中的值，同时遵守Java中对象不可变的




### int 和 Integer 自动装箱和自动拆箱


在Java中，数字字面量实际上是被当作基本数据类型处理的，而不是`Integer`对象。例如，当你写下一个数字如`5`时，它是被当作`int`类型而不是`Integer`类型。Java使用基本数据类型（如`int`, `double`, `float`, `long`等）和它们的包装类（如`Integer`, `Double`, `Float`, `Long`等）区分开处理。

- **基本数据类型**：这些类型存储数值数据，操作速度快，因为它们直接存储在栈上。它们不是对象，不支持方法调用，并且没有关联的方法。
    
- **包装类（Wrapper Classes）**：每个基本数据类型都有一个对应的包装类。这些类的实例可以存储为对象，允许将基本类型作为对象处理，例如在需要对象而不是基本类型的数据结构中使用。包装类存在于堆上，并且提供了一系列方法来操作数据。
    

例如，使用基本数据类型`int`和其包装类`Integer`：


```java
int basic = 5;       // 基本数据类型 

Integer wrapped = 5; // 自动装箱将int转换成Integer对象
```


关于自动装箱和拆箱：

- **自动装箱（Autoboxing）**：Java编译器自动将基本类型转换为相应的包装类对象。比如从`int`到`Integer`。
- **自动拆箱（Unboxing）**：Java编译器自动将包装类对象转换回基本类型。比如从`Integer`到`int`。

因此，在你的代码示例中，当你从`HashMap`中检索一个值时，如果该值被存储为`Integer`，那么在需要的时候它会自动拆箱成`int`类型进行运算。但你不能直接对一个`Integer`引用进行自增，因为它是一个不可变对象。这就是为什么需要显示地使用`map.put()`来更新其值的原因。

### 基本类和包装类

Java有八种基本数据类型，每种基本类型都有一个对应的包装类。下面是这些基本数据类型及其对应的包装类：

1. **byte** - 基本数据类型用于存储较小范围的整数值。
    
    - **装箱后的对象：** `Byte`
2. **short** - 用于存储较小的整数，范围比`byte`大。
    
    - **装箱后的对象：** `Short`
3. **int** - 用于存储普通范围的整数。这是默认的整数类型。
    
    - **装箱后的对象：** `Integer`
4. **long** - 用于存储大范围的整数。
    
    - **装箱后的对象：** `Long`
5. **float** - 用于存储单精度（32位）浮点数。
    
    - **装箱后的对象：** `Float`
6. **double** - 用于存储双精度（64位）浮点数。这是默认的浮点数类型。
    
    - **装箱后的对象：** `Double`
7. **char** - 用于存储单个16位的Unicode字符。
    
    - **装箱后的对象：** `Character`
8. **boolean** - 用于存储真值，即`true`或`false`。
    
    - **装箱后的对象：** `Boolean`

这些包装类不仅允许基本类型作为对象使用（例如在集合类中，如`ArrayList`只能存储对象），还提供了各种实用方法，例如比较、转换、以及与其他基本类型的互转等。通过自动装箱和拆箱，Java使得在基本类型和包装类之间的转换变得无缝和透明。