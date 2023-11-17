# 21. Merge Two Sorted Lists
###### `easy` `linked list` `recursion`

You are given the heads of two sorted linked lists list1 and list2.

Merge the two lists into one sorted list. The list should be made by splicing together the nodes of the first two lists.

Return the head of the merged linked list.



Example 1:
> Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]

Example 2:
> Input: list1 = [], list2 = []
Output: []

Example 3:
> Input: list1 = [], list2 = [0]
Output: [0]

---

**此題為遞迴、非遞回與間接指標不同寫法的練習**

## C

### Recursive 

Intuitive and easy to understand

```cpp
struct ListNode* mergeTwoLists(struct ListNode* list1, struct ListNode* list2) {
    if (!list1) 
        return list2;
    else if (!list2) 
        return list1;
    else {
        if (list1->val <= list2->val) {
            list1->next = mergeTwoLists(list1->next, list2);
            return list1;
        }
        list2->next = mergeTwoLists(list1, list2->next);
        return list2;
    }
}

```

### Non-recursive

Using a dummy listnode(head) to simplify the content of the while loop.

```cpp
struct ListNode* mergeTwoLists(struct ListNode* list1, struct ListNode* list2) {
    struct ListNode head;
    struct ListNode* cur = &head;
    while (list1 && list2) {
        if (list1->val <= list2->val) {
            cur->next = list1;
            list1 = list1->next;
        } else {
            cur->next = list2;
            list2 = list2->next;
        }
        cur = cur->next;
    }

    cur->next = list1 ? list1 : list2;
    return head.next;
}
```

### Indirect pointer 1

* Using an indirect pointer to avoid using a dummy listnode. 
* The indirect pointer is a pointer to the address for storing the next smaller listnode.
  * indirect pointer -> a pointer that is going point to list1/list2 -> list1/list2 
  * We obtain the next address to be updated through the indirect pointer, then update the content of the reference address with the address of the qualified listnode.

```cpp
struct ListNode* mergeTwoLists(struct ListNode* list1, struct ListNode* list2) {
    struct ListNode* head;
    struct ListNode** indirect = &head;
    while (list1 && list2) {
        if (list1->val <= list2->val) {
            *indirect = list1;
            list1 = list1->next;
        } else {
            *indirect = list2;
            list2 = list2->next;
        }
        indirect = &(*indirect)->next;
    }
    *indirect = list1 ? list1 : list2;
    return head;
}
```

### Indirect pointer 2

A more concise version using one more variable to eliminate the if-else condition.

```cpp
struct ListNode* mergeTwoLists(struct ListNode* list1, struct ListNode* list2) {
    struct ListNode* head;
    struct ListNode** indirect = &head;
    while (list1 && list2) {
        struct ListNode** cur = list1->val <= list2->val ? &list1: &list2;
        *indirect = *cur;
        *cur = (*cur)->next;
        indirect = &(*indirect)->next;
    }
    *indirect = list1 ? list1 : list2;
    return head;
}
```
