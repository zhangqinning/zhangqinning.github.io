---
layout: post
title: LeetCode [Swap Nodes in Pairs]
categories: [LeetCode]
description: 链表基础
keywords: LeetCode,Swap Nodes in Pairs,链表
---

Given a linked list, swap every two adjacent nodes and return its head.

For example,
Given 1->2->3->4, you should return the list as 2->1->4->3.

Your algorithm should use only constant space. You may not modify the values in the list, only nodes itself can be changed.

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
```

这题本来很简单，头一回不小心写错了，记录下来。常规写法多记录一个head。

递归：

```java
public class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head==null || head.next==null) return head;
        ListNode temp = head.next;
        head.next = swapPairs(head.next.next);
        temp.next = head;
        return temp;
    }
}
```

