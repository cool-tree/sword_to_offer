**JZ6** **从尾到头打印链表**

输入一个链表的头节点，按链表从尾到头的顺序返回每个节点的值（用数组返回）。

如输入{1,2,3}的链表如下图:
![image](https://user-images.githubusercontent.com/47242566/155068237-d0b58ce9-3175-4616-974b-5030a6de9d26.png)


返回一个数组为[3,2,1]

0 <= 链表长度 <= 10000

①个人考虑

​	（1）测试案例：空串，只有一个节点的串

​	（2）方案：1.申请的一个堆栈，遍历链表并将元素入栈，再依次将栈中元素输出，时间复杂和空间复杂都是O(n)。2.将链表倒置链接，具体过程如下图所示

![mmexport1645425127443](https://user-images.githubusercontent.com/47242566/155068507-1bb2afc4-77fe-4d14-b954-f8f6a8b37f5e.jpg)

```c++
/**
*  struct ListNode {
*        int val;
*        struct ListNode *next;
*        ListNode(int x) :
*              val(x), next(NULL) {
*        }
*  };
*/
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        vector<int> ans;
        if(head==NULL)
            return ans;
        
        ListNode* p1=head->next;
        ListNode* p2=p1->next;
    	bool is_first=true;
        
        while(p1!=NULL){
            p1->next=head;
            head=p1;
            p1=p2;
            if(p1!=NULL)
                p2=p1->next;
            if(is_first){
                is_first=false;
                head->next->next=NULL;
            }
        }
        
    	p1=head;
    	while(p1!=NULL){
            ans.push_back(p1->val);
            p1=p1->next;
        }
        return ans;
    }
};
```

问题：本题需要返回一个vector，因此空间复杂度并不能降低，如果单纯实现功能则可降低时间复杂度，另外循环中操作较多，导致时间复杂度也比较高
