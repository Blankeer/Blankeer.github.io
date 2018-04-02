---
title: 20--Valid-Parentheses
tags: [LeetCode]
date: 2017.12.20 22:06
categories: 刷题
---
https://leetcode.com/problems/valid-parentheses/description/
输入: 字符串包含 `{}[]()`
处理:成对出现
输出:是否成对
思路:
就是一个算数表达式成对问题,栈解决就好了
```java
class Solution {
    public boolean isValid(String s) {
        List<Character> stack=new ArrayList();
        for(int i=0;i<s.length();i++){
            char c=s.charAt(i);
            if(i>0&&stack.size()>0&&isMarch(stack.get(stack.size()-1),c)){
                stack.remove(stack.size()-1);
            }else{
                if(c==')'||c=='}'||c==']'){//如果某步加入的是后者,那么肯定是不匹配的
                    return false;
                }
                stack.add(c);
            }
        }
        return stack.size()==0;
    }
    private boolean isMarch(char a,char b){
        if(a+b=='('+')'){
            return true;
        }
        if(a+b=='{'+'}'){
            return true;
        }
        if(a+b=='['+']'){
            return true;
        }
        return false;
    }
}
```
