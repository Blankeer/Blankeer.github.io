---
title: 7--Reverse-Integer
tags: [LeetCode]
date: 2017.11.13 22:55
categories: 刷题
---
https://leetcode.com/problems/reverse-integer/description/
输入: 32位有符号 int
输出: 逆序
思路:
循环取余就可以实现了,需要注意溢出的情况
```java
class Solution {
    public int reverse(int x) {
        int t = x;
        int num = 0;
        while(t!=0){
            int temp = num*10 + t%10;
            if((temp-t%10)/10 != num){
                return 0;
            }
            num = temp;
            t=t/10;
        }
        return num;
    }
}
```
