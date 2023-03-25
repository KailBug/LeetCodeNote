Given a circular linked list, implement an algorithm that returns the node at the beginning of the loop.

Circular linked list: A (corrupt) linked list in which a node's next pointer points to an earlier node, so as to make a loop in the linked list.

Example 1:

Input: head = [3,2,0,-4], pos = 1
Output: tail connects to node index 1
Example 2:

Input: head = [1,2], pos = 0
Output: tail connects to node index 0
Example 3:

Input: head = [1], pos = -1
Output: no cycle

Follow Up:
Can you solve it without using additional space?

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/linked-list-cycle-lcci
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

####Solution1 in C++

**Hash table**.A very intuitive idea is: we traverse each node in the linked list and record it; once we encounter a previously traversed node, we can determine that there is a ring in the linked list. It can be easily implemented with the help of a hash table.

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode *> visited;
        while (head != nullptr) {
            if (visited.count(head)) {
                return head;
            }
            visited.insert(head);
            head = head->next;
        }
        return nullptr;
    }
};
```

####Solution2 in c++

**Fast and slow pointer**.We use two pointers, th *fast* and *slow* pointers.They all start at the head of the linklist.The *slow* pointer then moves backwards one position at a time, while the *fast* pointer moves backwards two positions.If there is a ring in the linklist, the *fast* pointer will eventually meet up again with the *slow* pointer in the ring.

As shown in the figure below, let the length of the outer part of the ring in the chain table be **a**.The *slow* pointer enters the ring and then travels a distance of **b** to meet *fast* poiner. At this point, the *fast* pointer has traveled trough the ring of **n** turns, so the total distance it has traveled is *a + n(b + c) + b = a + (n + 1)b + nc*

![](https://assets.leetcode-cn.com/solution-static/jindian_02.08/jindian_02.08.png)

According to the question, the distance traveled by the fast pointer at any given moment is the distance traveled by the slow pointer 2 times. Therefore, we have:*a + (n + 1)b + nc = 2(a+b) **⟹** a = c + (n−1)(b+c)*

With *a = c + (n - 1)(b + c)*, we will find:  Distance from the meeting point to the entry point plus **n-1** turns of the ring length, which is exactly equal to the distance from the head of the chain to the entry point.

So, when slow pointer is found to meet fast pointer, we then use an additional pointer ptr. start, which points to the head of the chain; subsequently, it and slow moves backwards one position at a time. Eventually, they meet at the loop entry point.


```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *slow = head, *fast = head;
        while (fast != nullptr) {
            slow = slow->next;
            if (fast->next == nullptr) {
                return nullptr;
            }
            fast = fast->next->next;
            if (fast == slow) {
                ListNode *ptr = head;
                while (ptr != slow) {
                    ptr = ptr->next;
                    slow = slow->next;
                }
                return ptr;
            }
        }
        return nullptr;
    }
};
```