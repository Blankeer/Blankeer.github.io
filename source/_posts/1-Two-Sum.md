---
title: 1--Two-Sum
tags: [LeetCode]
date: 2017.11.08 23:12
categories: 刷题
---
https://leetcode.com/problems/two-sum/description/
输入: nums 数组 ,和 target 整型
处理: 找到 nums 中两个数字相加等于 target
输出: 输入这两个数字的下标

- 思路1
循环两次,判断相加是否等于 target
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for(int i=0;i<nums.length-1;i++){
            for(int j=i+1;j<nums.length;j++){
                if(nums[i]+nums[j] == target) {
                    return new int[]{i,j};
                }
            }
        }
        return new int[]{-1,-1};
    }
}
```
- 思路2
看了讨论才想到的,惭愧,只需要 O(n),利用 HashMap
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer,Integer> map = new HashMap<Integer,Integer>();
        for(int i=0;i<nums.length;i++){
            if(map.containsKey(target-nums[i])){
                return new int[]{map.get(target-nums[i]),i};
            }
            map.put(nums[i],i);
        }
        return new int[]{-1,-1};
    }
}
```

类似数字对应的题目都可以考虑用下哈希表,可能会有很好的做法
