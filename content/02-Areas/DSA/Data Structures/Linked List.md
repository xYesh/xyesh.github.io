---
{"aliases":null,"tags":null,"publish":true,"date created":"2025-01-14T15:25","date modified":"2025-01-14T17:29","PassFrontmatter":true,"created":"2025-01-14T15:25:42.504+05:30","updated":"2025-01-14T17:29:56.848+05:30"}
---


# Reversing a Linked List
- You need to keep returning the end of the list
	- i.e., Keep returning the first "next" variable as that is the final solution.
	- You do a `head.next.next = head` because you are telling your next to refer back to yourself. this is the act of reversing.
```Java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode next = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return next;
    }
}
```
