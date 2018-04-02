---
title: 14--Longest-Common-Prefix
tags: [LeetCode]
date: 2017.11.16 22:10
categories: 刷题
---
https://leetcode.com/problems/longest-common-prefix/discuss/
输入: 字符串数组
输出: 求公共最长前缀字符串

思路:
先取出第0个 string, 依次比较后面的字符串,求公共前缀
```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if(strs.length ==0 ){
            return "";
        }
        StringBuilder sb=new StringBuilder();
        for(int i=0;i<strs[0].length();i++){
            char c=strs[0].charAt(i);
            boolean isbreak = false;
            for(int j=1;j<strs.length;j++){
                if(strs[j].length()<=i||strs[j].charAt(i)!=c){
                    isbreak=true;
                    break;
                }
            }
            if(isbreak){
                break;
            }else{
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```
