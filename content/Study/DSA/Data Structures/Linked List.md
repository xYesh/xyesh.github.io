---
{"publish":true,"PassFrontmatter":true,"created":"2024-12-03T20:38:59.494+05:30","updated":"2024-12-26T08:34:11.366+05:30"}
---


# Reversing a linked list
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

