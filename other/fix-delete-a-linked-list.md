---
description: 'free(): double free detected in tcache 2'
---

# fix: delete a linked list

**Environment**

* IDE: vscode
* Compiler: `g++ (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0`

**Error message**

```cpp
free(): double free detected in tcache 2
Aborted
```

**Context**

```cpp
// Free memory of a linked list given head node
void DestroyList(ListNode* head) {
    ListNode* curr = head;
    while (curr != nullptr) {
        head = head->next;
        delete head;
        curr = head;
    }
}
```

```cpp
// Caller
void Test3() {
    int data[] = {1, 2, 3, 4, 5};
    ListNode* node = CreateList(data, 5);
    Test("Test 3", node, node, node);
    
    DestroyList(node);
}
```

**Causes**

Mess logic flow of loop body. Deleting the memory pointed to by the pointer to the head node twice.

#### Fix

```cpp
void DestroyList(ListNode* head) {
    while (head != nullptr) {
        ListNode* tmp = head;
        head = head->next;
        delete tmp;
    }
}
```

A better way:

```cpp
void DestroyList(ListNode** head) {
    while (*head != nullptr) {
        ListNode* tmp = *head;
        *head = (*head)->next;
        delete tmp;
    }
}
```

If in this way, the function is called like

```cpp
DestroyList(&head);
```

Then the pointer to `head` node will be equal to `nullptr`.

**Reference**

[what does this error means : "free(): double free detected in tcache 2"](https://stackoverflow.com/questions/67359233/what-does-this-error-means-free-double-free-detected-in-tcache-2)
