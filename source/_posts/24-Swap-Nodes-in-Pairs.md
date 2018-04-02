---
title: 24--Swap-Nodes-in-Pairs
tags: [LeetCode]
date: 2018.01.16 23:25
categories: 刷题
---
https://leetcode.com/problems/swap-nodes-in-pairs/description/
输入: 一个链表
输出: 每两位颠倒
要求: 不能使用额外空间,不能修改节点的值,只能修改引用

思路: 循环修改 next 即可,一次 AC
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
    public ListNode swapPairs(ListNode head) {
        ListNode current=head,next,nextnext;
        ListNode result=new ListNode(-1);
        ListNode pre=result;
        result.next = head;
        while(current!=null&&(next=current.next)!=null){
            nextnext = next.next;
            pre.next=next;
            next.next=current;
            current.next=nextnext;
            pre=current;
            current = nextnext;
        }
        return result.next;
    }
}
```
