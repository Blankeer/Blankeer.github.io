---
title: 21--Merge-Two-Sorted-Lists
tags: [LeetCode]
date: 2017.12.21 21:31
categories: 刷题
---
https://leetcode.com/problems/merge-two-sorted-lists/description/
输入: 两个排好序的链表
输出: 两个链表连接起来,并且是排好序的

思路:
循环遍历两个链表,判断当前节点的大小,需要注意长度不等
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
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1==null){
            return l2;
        }
        if(l2==null){
            return l1;
        }
        ListNode res = new ListNode(-1);
        ListNode i=l1,j=l2,item=res;
        do{
            if(i!=null&&j!=null){
                if(i.val<j.val){
                    item.next=i;
                    i=i.next;
                    item=item.next;
                }else{
                    item.next=j;
                    j=j.next;
                    item=item.next;
                }
            }else if(j==null){
                item.next=i;
                i=i.next;
                item=item.next;
            }else if(i==null){
                item.next=j;
                j=j.next;
                item=item.next;
            }

        }while(i!=null||j!=null);
        return res.next;
    }
}
```
