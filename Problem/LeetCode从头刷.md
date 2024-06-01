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

使用水塘抽样算法的做法：
