---
title: 2--Add-Two-Numbers
tags: [LeetCode]
date: 2017.11.08 23:41
categories: 刷题
---
https://leetcode.com/problems/add-two-numbers/description/
输入: 两个链表,每个节点上有一个 val 值
处理: 将对应节点的值加起来,结果保留个位,如果有进位,加到下一个节点上
输出: 相加的结果链表

思路:
加起来就行了,需要记录相加的值,并和10相除取余保存处理.
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode result = new ListNode(-1);
        ListNode temp1 = l1;
        ListNode temp2 = l2;
        ListNode temp3 = result;
        int num=0;
        while(temp1!=null || temp2!=null) {
            if(temp1!=null) {
                num+=temp1.val;
                temp1 = temp1.next;
            }
            if(temp2!=null) {
                num+=temp2.val;
                temp2 = temp2.next;
            }
            ListNode item = new ListNode(num%10);
            temp3.next = item;
            temp3 = temp3.next;
            num=num/10;
        }
        if(num!=0){
            ListNode item = new ListNode(num);
            temp3.next = item;
        }
        return result.next;
    }
}
```
