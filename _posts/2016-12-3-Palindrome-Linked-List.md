---
layout: post
title: LeetCode [Palindrome Linked List]
categories: [LeetCode]
description: 链表基础
keywords: LeetCode，Palindrome Linked List,链表
---

Given a singly linked list, determine if it is a palindrome.

Follow up:
Could you do it in O(n) time and O(1) space?

关于回文的老问题，这题针对链表,解法如下：

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; 
 * }
 */
public class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode fast = head,slow = head;
        while(fast!=null&& fast.next!=null){
            fast = fast.next.next;
            slow = slow.next;
        }
        if(fast!=null){
            slow = slow.next;
        }
        slow = reverse(slow);
        fast = head;
        
        while(slow!=null){
            if(fast.val != slow.val) return false;
            slow = slow.next;
            fast = fast.next;
        }
        return true;
    }
    public ListNode reverse(ListNode head){
        ListNode pre = null;
        while(head != null){
            ListNode next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
        return pre;
    }
}
```

