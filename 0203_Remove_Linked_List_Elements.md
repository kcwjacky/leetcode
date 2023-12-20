# 203. Remove Linked List Elements
###### `medium` `linked list` `recursion` `indirect pointer`

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Example 1:
> Input: l1 = [2,4,3], l2 = [5,6,4] <br>
Output: [7,0,8] <br>
Explanation: 342 + 465 = 807.

Example 2:
> Input: l1 = [0], l2 = [0] <br>
Output: [0]

Example 3:
> Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9] <br>
Output: [8,9,9,9,0,0,0,1]

---

## C

### recursion 

```cpp
struct ListNode* removeElements(struct ListNode* head, int val) {
    if (!head)
        return NULL;

    struct ListNode *node = removeElements(head->next, val);
    if (head->val == val) {
        return node;
    }
    head->next = node;
    return head;
}
```

### dummy ListNode 

```cpp
struct ListNode* removeElements(struct ListNode* head, int val) {
    struct ListNode dummy = {0, head};
    struct ListNode *cur = &dummy;

    while (cur && cur->next) {
        if (cur->next->val == val) {
            cur->next = cur->next->next;
        } else {
            cur = cur->next;
        }
    }
    return dummy.next;
}
```

### indirect pointer 

```cpp
struct ListNode* removeElements(struct ListNode* head, int val) {
    struct ListNode **indirect = &head;

    while (*indirect) {
        if ((*indirect)->val == val) 
            *indirect = (*indirect)->next;
        else
            indirect = &((*indirect)->next);
    }
    return head;
}
```