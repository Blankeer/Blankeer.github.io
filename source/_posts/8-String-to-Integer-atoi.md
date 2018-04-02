---
title: 8--String-to-Integer-(atoi)
tags: [LeetCode]
date: 2017.11.14 22:50
categories: 刷题
---
https://leetcode.com/problems/string-to-integer-atoi/discuss/
实现 atoi 函数
思路:
循环按位解析,需要手动判断溢出等情况,写的有点乱,之后再精简下.
```java
class Solution {
    public int myAtoi(String str) {
        str = str.trim();
        if(str.length() == 0){
            return 0;
        }
        int number=0;
        int fu=1;
        int startIndex=0;
        if(str.charAt(0)=='-'){
            fu=-1;
            startIndex=1;
        }else if(str.charAt(0)=='+'){
            startIndex=1;
        }
        for(int i=startIndex;i<str.length();i++){
            char c=str.charAt(i);
            if(isNumber(c)){
                int n=c-'0';
                if(fu==1 && ( number*10>=(Integer.MAX_VALUE-n) || number>Integer.MAX_VALUE/10 )){
                    number=Integer.MAX_VALUE;
                    return number;
                }else if(fu==-1 && (-10*number<=(Integer.MIN_VALUE+n)|| number>Integer.MIN_VALUE/-10 )){
                    number=Integer.MIN_VALUE;
                    return number;
                }
                number=number*10+n;
            }else{
                break;
            }
        }    
        return fu*number;
    }

    private boolean isNumber(char c){
        return c>='0'&&c<='9';
    }
}
```
