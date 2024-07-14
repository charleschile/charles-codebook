### 

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> hash = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (hash.containsKey(target - nums[i])) {
                return new int[] {i, hash.get(target - nums[i])};
            }
            hash.put(nums[i], i);
        }
        return null;
    }
}
```




### [398. Random Pick Index](https://leetcode.cn/problems/random-pick-index/)

暴力使用哈希表的做法：

```java
class Solution {
    private Map<Integer, List<Integer>> hash;
    private Random random;

    public Solution(int[] nums) {
        hash = new HashMap<>();
        random = new Random();
        for (int i = 0; i < nums.length; i++) {
            if (!hash.containsKey(nums[i])) {
                hash.put(nums[i], new ArrayList<>());
            }
            hash.get(nums[i]).add(i);
        }
    }
    
    public int pick(int target) {
        List<Integer> indices = hash.get(target);
        return indices.get(random.nextInt(indices.size()));
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(nums);
 * int param_1 = obj.pick(target);
```

使用水塘抽样算法的做法：算法参考请使用[[算法学习#01 水塘抽样reservoir sampling]]

```java
class Solution {
    private int[] nums;
    Random random;

    public Solution(int[] nums) {
        this.nums = nums;
        this.random = new Random();
    }
    
    public int pick(int target) {
        int ans = 0;
        for (int i = 0, cnt = 0; i < nums.length; i++) {
            if (nums[i] == target) {
                cnt++;
                if (random.nextInt(cnt) == 0) {
                    ans = i;
                }
            }
        }
        return ans;

    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(nums);
 * int param_1 = obj.pick(target);
 */
```