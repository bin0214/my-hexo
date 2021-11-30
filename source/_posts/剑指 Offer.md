---
title: 剑指 Offer
date: 2021/10/20 14:00:00
updated: 2021/11/30 10:22:00
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

![](/img/剑指Offer/04-1)

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

## 剑指 Offer 05. 替换空格

请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。

**示例1：**

```
输入：s = "We are happy."
输出："We%20are%20happy."
```

### 题解

1. 初始化一个 StringBuilder（res）
2. 遍历字符串s中每个字符c
   - 当c为空格时：向 res 后添加字符串 "%20" ；
   - 当c不为空格时：向 res 后添加字符串 c ；
3. 将列表 res 转化为字符串并返回。

### 代码

```java
class Solution {
    public String replaceSpace(String s) {
        //初始化一个 StringBuilde
        StringBuilder sb = new StringBuilder();
        //遍历字符串s中每个字符c
        for(int i = 0;i<s.length();i++){
            if(s.charAt(i) == ' '){//当c为空格时：向 res 后添加字符串 "%20" ；
                sb.append("%20");
            }else{//当c不为空格时：向 res 后添加字符串 c ；
                sb.append(s.charAt(i));
            }
        }  
        //将列表 res 转化为字符串并返回。
        return sb.toString();
    }
}
```

## 剑指 Offer 06. 从尾到头打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

**示例1：**

```
输入：head = [1,3,2]
输出：[2,3,1]
```

### 题解

栈的特点是后进先出，即最后压入栈的元素最先弹出。考虑到栈的这一特点，使用栈将链表元素顺序倒置。从链表的头节点开始，依次将每个节点压入栈内，然后依次弹出栈内的元素并存储到数组中。

### 代码

```java
class Solution {
    public int[] reversePrint(ListNode head) {
        //创建一个栈
        Stack<Integer> sta = new Stack<>();
        ListNode temp =head;
        //入栈
        while(temp != null){
            sta.add(temp.val);
            temp = temp.next;
        }
        int[] res =new int[sta.size()];
        int i =0;
        //出栈
        while(!sta.isEmpty()){
            res[i++] = sta.pop();
        }
        return res;
    }
}
```

## 剑指 Offer 07. 重建二叉树

输入某二叉树的前序遍历和中序遍历的结果，请构建该二叉树并返回其根节点。

假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

**示例1：**

![](/img/剑指Offer/07-1)

```
Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]
```

### 题解

### 代码
