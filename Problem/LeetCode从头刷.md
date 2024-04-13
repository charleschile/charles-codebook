## 与C++刷题的区别
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

// 数组
return new int[] {i, x, 3};
return new int[0]; // 注意这里只能是int[0]后面不用跟括号！
int[] array = new int[256];


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

### [1. 两数之和](https://leetcode.cn/problems/two-sum/)

> 注意哈希表的声明，containsKey, get, put各种api

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> hash = new HashMap<Integer, Integer>();
        for (int i = 0; i < nums.length; i++) {
            int x = nums[i];
            if (hash.containsKey(target - x)) {
                return new int[] {i, hash.get(target - x)};
            }
            hash.put(x, i);
        }
        return new int[0];
    }
}
```



### [2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)

 ```java
 /**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1);
        // l1，l2没有必要恢复原状，所以直接使用l1和l2
        int carry = 0;
        ListNode curr = dummy;
        while (carry != 0 || l1 != null || l2 != null) {
            if (l1 != null) {
                carry += l1.val;
                l1 = l1.next;
            }
            if (l2 != null) {
                carry += l2.val;
                l2 = l2.next;
            }
            int value = carry % 10;
            curr.next = new ListNode(value);
            curr = curr.next;
            carry /= 10;
        }
        return dummy.next;
    }
}
 
```



### 