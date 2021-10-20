---
title: 剑指 Offer 03. 数组中重复的数字
date: 2021/10/20 14:00:00
categories:
- LeetCode
tags:
- 刷题
- 剑指 Offer
- 哈希表
---

## 剑指 Offer 03. 数组中重复的数字

找出数组中重复的数字。

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

**示例 1：**

```
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```

### 题解

- 先对数组进行排序，看相邻元素是否有相同的，相同直接return。
- 使用Hash/Set ，如果不存在Hash/Set中，就将该数加入到集合中，否则就输出该数

### 代码

#### 排序

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        Arrays.sort(nums);
        for(int i = 0 ;i<nums.length;i++){
            if(nums[i] == nums[i+1]){
                return nums[i];
            }
        }
        return -1;
    }
}
```

#### 哈希表

```
class Solution {
    public int findRepeatNumber(int[] nums) {
        HashSet<Integer> set = new HashSet<>();
        for(int i = 0;i<nums.length;i++){
            if(!set.contains(nums[i])){
                set.add(nums[i]);
            }else{
                return nums[i];
            }
        }
        return -1;
    }
}
```

