---
title: 3--Longest-Substring-Without-Repeating-Characters
tags: [LeetCode]
date: 2017.11.09 22:26
categories: 刷题
---
https://leetcode.com/problems/longest-substring-without-repeating-characters/description/
输入: 字符串
处理: 寻找不包含重复字符的最长子串
输出: 最长子串的长度

思路:
1.左指针始终指向当前子串的最左边,
2.判断当前字符是否包含在当前子串中,如果在,左指针等于前面重复字符的下标+1,
3.否则继续判断下一位字符
4.记录每次子串长度的最大值
其中第2步,可以简化为 `当前左指针下标` 和 `重复字符下标+1` 的最大值,因为: 如果当前字符不包含在当前子串中,而是在子串之前,求个最大值,也没什么不妥,代码看起来简洁很多.
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        HashMap<Character,Integer> map = new HashMap<Character,Integer>();
        int max=0;
        int left=0;
        for(int i=0;i<s.length();i++){
            char c=s.charAt(i);
            if(map.containsKey(c)){
                left=Math.max(left,map.get(c)+1);
            }
            max = Math.max(max,i-left+1);
            map.put(c,i);
        }
        return max;
    }
}
```
