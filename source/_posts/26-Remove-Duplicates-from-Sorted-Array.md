---
title: 26--Remove-Duplicates-from-Sorted-Array
tags: [LeetCode]
date: 2018.01.18 22:05
categories: 刷题
---
输入: 排好序的数组
输出: 返回不重复的个数,并修改数组为不重复的数组,要求不能使用额外的空间
思路: 循环判断前一个和当前是否相等,循环移动位置.
```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums.length==0){
            return 0;
        }
        int t=nums[0],res=nums.length;
        int l=0,r=-1,count=0;
        for(int i=1;i<nums.length-count;i++){
            int item = nums[i];
            if(item != t){
                t = item;
                r = i-1;
                int len=r-l;
                if(len>=1){
                    move(nums,l,r);
                    count+=len;
                }
                i-=len;
                l = i;
            }
        }
        r = nums.length-count-1;
        int len=r-l;
        if(len>=1){
            move(nums,l,r);
            count+=len;
        }
        return res - count;
    }

    private void move(int[] nums,int l,int r){
        int len=r-l;
        for(int i=l+1;i<nums.length-len;i++){
            nums[i]=nums[i+len];
        }
    }
}
```
