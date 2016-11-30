---
layout: post
title: LeetCode [Rotate List]
categories: [LeetCode]
description: 链表基础
keywords: LeetCode，Reverse Nodes in k-Group,链表
---

Given a list, rotate the list to the right by k places, where k is non-negative.

For example:
Given 1->2->3->4->5->NULL and k = 2,
return 4->5->1->2->3->NULL.


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
常规的解法，目前没有找到有意思的解法：

```java
public class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if( head==null || k == 0) return head;
        ListNode node = head;
        int len = 1;
        while(node.next !=null) {
            len++;
            node = node.next;
        } 
        k %= len;
        node.next = head;
        for(int i = 0;i<len -k ;i++){
            node = node.next;
        }
        head = node.next;
        node.next = null;
        return head;
    }
}
```

