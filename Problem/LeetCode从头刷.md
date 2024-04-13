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