题目描述
======================
给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。
你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

示例:

给定 1->2->3->4, 你应该返回 2->1->4->3.

![](https://pic.leetcode-cn.com/763b502bd7d33405e1f24473b509a4441fa24f5d074f2b0f2649b5842bce6712-leetcode24swapnodeinparis.JPG)
解法
============================
## 循环
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode * dummHead = new ListNode(0);
        dummHead->next = head;

        ListNode *p = dummHead;
        while(p->next && p->next->next){
            ListNode * node1 = p->next;
            ListNode * node2 = node1->next;
            ListNode * next = node2->next;

            node2->next = node1;
            node1->next = next;
            p->next = node2;
            
            p = node1;
        }

        ListNode *retNode = dummHead->next;
        delete dummHead;

        return retNode;
    }
};
```
## 递归
```c
struct ListNode* swapPairs(struct ListNode* head){
    if(head==0||head->next==0) return head;
    struct ListNode* odd=head, * even=odd->next;
    head=even;
    
    odd->next=even->next;
    even->next=odd;
    odd->next=swapPairs(odd->next);

    return head;
}
```