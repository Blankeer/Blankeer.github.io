---
title: 22--Generate-Parentheses
tags: [LeetCode]
date: 2018.01.04 21:54
categories: 刷题
---
https://leetcode.com/problems/generate-parentheses/description/
输入: 数字 n
处理: n 对括号()的排列
输出: 所有成对的结果
思路: n 对括号,字符串长度就是2*n,每位是`(`或`)`,由于 n 是参数动态的,所以不能采取循环,只能采取递归回溯实现,过程中需要剪枝.预处理左括号是1,右括号是-1,这样只要最终每位之和等于0就匹配了
剪枝条件:
1. 当前总和=0时,下一位只能是1,因为如果是-1后面再多都不会匹配
2. 当前总和<0时,直接剪枝
3. 当前总和>n时,直接剪枝,因为后面即使都是-1,总和也是大于0的
4. 当前总和=n时,下一位只能是-1,因为如果是1,总和不会=0

```java
class Solution {

    char[] arr={'(',')'};
    int[] values={1,-1};
    List<String> res;

    public List<String> generateParenthesis(int n) {
        res= new ArrayList<String>();
        fun(0,n,0,new StringBuilder());
        return res;
    }
    private void fun(int i,int n,int sum,StringBuilder sb){
        if(i==n*2){
            if(sum==0){
                res.add(sb.toString());
            }
            return;
        }
        if(sum == 0){
            sum = call(i,arr[0],values[0],sb,sum,n);
        }else if(sum>0){
            if(sum > n){
                return;
            }else if(sum < n){
                sum = call(i,arr[0],values[0],sb,sum,n);
            }
            sum = call(i,arr[1],values[1],sb,sum,n);
        }

    }
    private int call(int i,char c,int val,StringBuilder sb,int sum,int n){
        sb.append(c);
        sum+=val;
        fun(i+1,n,sum,sb);
        sb.deleteCharAt(sb.length()-1);
        sum-=val;
        return sum;
    }
}
```
