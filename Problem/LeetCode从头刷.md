

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



### [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

#### 方法一：使用普通数组
### 方法二：使用哈希表


### 方法三：使用哈希集合
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        // 使用双指针
        // i代表以i结尾的字符串
        // 使用哈希集合
        Set<Character> hash = new HashSet<Character>();
        int n = s.length();
        if (n == 0) return 0;
        int ans = 1;
        for (int i = 0, j = 0; i < n; i++) {
            char c = s.charAt(i);
            while (hash.contains(c)) {
                hash.remove(s.charAt(j));
                j++;
            }
            hash.add(c);
            ans = Math.max(ans, i - j + 1);
        }
        return ans;
    }
}
```