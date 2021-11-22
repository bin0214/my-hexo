---
title: 剑指 Offer
date: 2021/10/20 14:00:00
categories:
- LeetCode
tags:
- 刷题
- 剑指 Offer
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

## 剑指 Offer 04. 二维数组中的查找

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

![](/img/LeetCode/image-20211122110207993.png)

### 题解

由于给定的二维数组具备每行从左到右递增以及每列从上到下递增的特点，当访问到一个元素时，可以排除数组中的部分元素。

从二维数组的右上角开始查找。如果当前元素等于目标值，则返回 true。如果当前元素大于目标值，则移到左边一列。如果当前元素小于目标值，则移到下边一行。

可以证明这种方法不会错过目标值。如果当前元素大于目标值，说明当前元素的下边的所有元素都一定大于目标值，因此往下查找不可能找到目标值，往左查找可能找到目标值。如果当前元素小于目标值，说明当前元素的左边的所有元素都一定小于目标值，因此往左查找不可能找到目标值，往下查找可能找到目标值。

- 若数组为空，返回 false
- 初始化行下标为 0，列下标为二维数组的列数减 1
- 重复下列步骤，直到行下标或列下标超出边界
  - 获得当前下标位置的元素 num
  - 如果 num 和 target 相等，返回 true
  - 如果 num 大于 target，列下标减 1
  - 如果 num 小于 target，行下标加 1
- 循环体执行完毕仍未找到元素等于 target ，说明不存在这样的元素，返回 false

### 代码

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target){ 
        if(matrix == null || matrix.length ==0 || matrix[0].length == 0 ){
            return  false;
        }
        int rows = matrix.length;
        int columns  = matrix[0].length;
        int row = 0;
        int column  = matrix[0].length -1;
        while(rows > row  && column >=0){
            int num = matrix[row][column];
            if(num == target){
                return true;
            }
            if(num > target){
                column--;
            }else{
                row++;
            }
        }
        return false;
    }
}

```

