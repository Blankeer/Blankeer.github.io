---
title: 27--Remove-Element
tags: [LeetCode]
date: 2018.01.23 22:03
categories: 刷题
---
输入: 一个数组和一个数字
输出: 数组不包含该数字的长度,并修改原数组.
不能额外空间
思路: 循环遍历, l 为目标数组最右边元素,初始=0,当遇到 nums[i] != val 时, l++ 并赋值.
```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int l=0;
        for(int i=0;i<nums.length;i++){
            if(nums[i]!=val){
                nums[l]=nums[i];
                l++;
            }
        }
        return l;
    }
}
```
