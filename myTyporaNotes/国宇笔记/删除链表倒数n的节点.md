## 题目描述

给定一个链表，删除链表的倒数第n个节点并返回链表的头指针

```
给出的链表为:1->2->3->4->5, n= 2.
删除了链表的倒数第n个节点之后,链表变为1->2->3->5.
```

备注：

题目保证n一定是有效的
请给出请给出时间复杂度为O(n)的算法

示例1

##### 输入

```
{1,2},2
```

##### 输出

```
{2}
```

```c++
/**

 * struct ListNode {
 * int val;
 * struct ListNode *next;
 * };
	*/
class Solution {
public:
    /**
     * 
     * @param head ListNode类 
     * @param n int整型 
     * @return ListNode类
     */
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        // write code here
        ListNode *node = new ListNode(-1);
        node -> next = head;
        ListNode *fast = node;
        while(n-- >= 0)//fast!=NULL ---->return ;
            fast = fast->next;
        ListNode *slow = node;
        while(fast!=NULL){
            fast = fast->next;
            slow = slow->next;
        }
        slow->next = slow->next->next;
        return node->next;
    }
};
```

```c++
/**

 * struct ListNode {
 * int val;
 * struct ListNode *next;
 * };
	*/

class Solution {
public:
    /**
     * 
     * @param head ListNode类 
     * @param n int整型 
     * @return ListNode类
     */
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(head==nullptr) return nullptr;
        ListNode *p=head;
        ListNode *q=head;
        for(int i=0;i<n;i++){
            if(p->next==nullptr) return head->next;
            p=p->next;
        }
        while(p->next!=nullptr){
            p=p->next;
            q=q->next;
        }
        q->next=q->next->next;
        return head;
    }
};
```

 ```c++
//己据上修
/**
* struct ListNode {
* int val;
* struct ListNode *next;
* };
*/
class Solution {
public:
    /**
  *  @param head ListNode类 
  * @param n int整型 
  @return ListNode类
   */
    ListNode removeNthFromEnd(ListNode* head, int n) {
        // write code here
        ListNode * ptrLft=head;
        ListNode * ptrRgt=head;
        int cnt=0;
        while(cnt++<n){
            if(ptrRgt->next==NULL)
                return head->next;
            ptrRgt=ptrRgt->next;
        }
        while(ptrRgt->next!=NULL){
            ptrRgt=ptrRgt->next;
            ptrLft=ptrLft->next;
        }
        ptrLft->next=ptrLft->next->next;
        return head;
    }
};
 ```

​                

