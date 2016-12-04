---
layout: post
title: LeetCode [Reverse Linked List]
categories: [LeetCode]
description: 链表基础
keywords: LeetCode，Reverse Linked List,链表
---

Reverse a singly linked list.

常规解法和递归解法：

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode pre = null;
        while (head!=null)
        {
            ListNode next = head.next;
            head.next = pre;
            pre=head;
            head=next;
        }
        return pre;
    }

    public ListNode reverseList(ListNode head) {
        return reverse(head,null);
    }
    
    private ListNode reverse(ListNode head,ListNode pre){
        if(head == null) return pre;
        ListNode next = head.next;
        head.next = pre;
        return reverse(next,head);
    }
}
```


