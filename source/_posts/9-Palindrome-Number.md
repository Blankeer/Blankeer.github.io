---
title: 9--Palindrome-Number
tags: [LeetCode]
date: 2017.11.15 21:55
categories: 刷题
---
https://leetcode.com/problems/palindrome-number/discuss/
判断一个数字是否是回文数
思路:
将数字逆序,判断是否相等.
```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x<0){
            return false;
        }
        int res=0;
        int source=x;
        while(source!=0){
            res=res*10+source%10;
            source=source/10;
        }
        return x==res;
    }
}
```
上面的 ac 了...
看了下讨论,还有简单的做法.
回文数只有两种情况: 位数是偶数 or 奇数
位数是偶数情况:  abccba, 存在一个位置,使得左边的数字 abc 和右边数字 cba 互为逆数
位数是奇数情况: abcdcba,存在一个位置,使得左边的数字 abc 和右边数字 dcba 的逆数/10 相等
```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x==0){
            return true;
        }
        if(x<0 || x%10 == 0){
            return false;
        }
        int res=0;
        int source=x;
        while(source>res){
            res=res*10+source%10;
            source=source/10;
        }
        return source==res || source == res/10;
    }
}
```
