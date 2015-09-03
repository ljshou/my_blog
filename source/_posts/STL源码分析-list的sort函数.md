title: STL源码分析---list的sort函数
date: 2014-10-30 16:25:39
categories: STL源码分析
tags: STL
---
*《STL源码分析系列》*
---

面试的时候，可能面试官让你写一个链表的归并排序，但是要求**不能用递归**，
当然也不能用栈来模拟递归。STL 库中list容器底层其实就是一个双向链表，
今天就来研究下list的成员函数 sort。兼顾了效率与简洁，当然可读性相比
与递归版本差很多。
<!--more-->

### MergeSort 的递归版本
首先分析下 MergeSort 的递归版本是如何工作的。
+ 代码
```cpp
/*
struct ListNode{  
  int val;  
  ListNode *next;  
  ListNode(int x)  
    :val(x), next(NULL){}  
};
*/
//merge two sorted lists into a sorted list  
ListNode* merge(ListNode* &, ListNode* &);
ListNode *sortList(ListNode *head) {  
    if(head == NULL || head->next == NULL) return head;  
    //Divide: fast-slow scheme to divide list into two parts
    ListNode *fast(head->next), *slow(head), *list2(NULL);  
    while(fast && fast->next)  
    {  
        fast = fast->next->next;  
        slow = slow->next;  
    }  
    list2 = slow->next;  
    slow->next = NULL;
    //Conquer
    head = sortList(head);  
    list2 = sortList(list2);  
    return mergeList(head, list2);  
}
```

+ 分析：
考虑如下的例子，对一个长度为 8 的数组进行归并排序，如下图
![](http://ljshou.qiniudn.com/merge-sort-recur.png)

### MergeSort 的迭代版本
而 迭代版本恰恰相反，是 从下往上。
为了便于明白算法的思想，我模仿 STL 库的 list sort 重新写了归并排序。文章最后给出了 STL 的源码。
+ 代码：
```cpp
void ListSort(ListNode* & list)  
{  
  if(list == NULL || list->next == NULL) /* 空或者1个元素的链表 */  
    return;  

  ListNode *carry(NULL);  
  ListNode *counter[64] = {NULL}; /* 64个list, 中介数据中转站，用于模拟递归 */  
  int fill = 0;  
  while(list) {  
    /* insert first node of list into front of carry */  
    ListNode *node = list; list = list->next;  
    node->next = carry;  
    carry = node;  

    int i = 0;  
    while(i < fill && counter[i]) {  
      counter[i] = merge(counter[i], carry);  
      carry = NULL; /* after merge, carry is now empty */  
      swap(carry, counter[i++]);  
    }  
    swap(carry, counter[i]);  
    if(i == fill) ++fill;  
  }  

  for(int i = 1; i < fill; ++i){ /* 将 64 个 lists 归并成一个 list */  
    counter[i] = merge(counter[i], counter[i-1]);  
    counter[i-1] = NULL;  
  }  
  swap(list, counter[fill-1]);
}  
```

+ 代码分析：
上述代码中 开了一个长度为 64 的链表数组, 作为中转站。注意：还是常数空间。
第 i 个链表长度最长是 2^(i+1)。
算法的执行过程如下：
对 8 1 4 5 进行排序。
![](http://ljshou.qiniudn.com/list-sort.png)
比较 **递归与迭代的算法过程，可以发现两者就是互逆的过程**。
现在相比大家对算法有了一个全面的认识。STL 库正是 利用64个链表，实现了上图中的算法。
这个算法能够排序的总数是 2^65-2 个数，应该够用了。

###SGI STL 的源代码（选自 STL 源码分析）如下：
```cpp
// list 不能使用 STL 算法 sort ()  
// 因为 STL 算法 sort() 只接受 RandomAccessIterator  
// 本函数采用 mergesort  
template <class T, class Alloc>  
void list<T, Alloc>::sort () {  
  // 以下判断，如果是空链表， 或仅有一个元素，就不进行任何操作  
  if (node->next == node || link_type(node->next)->next == node)  
  return;  

  // 一些新的 lists, 作为中介数据存放区  
  list<T, Alloc> carry;  
  list<T, Alloc> counter[64];  
  int fill = 0;  
  while (!empty()) {  
    carry.splice(carry.begin(), *this, begin());  
    in i = 0;  
    while(i < fill && !counter[i].empty()) {  
      counter[i].merge(carry);  
      carry.swap(counter[i++]);  
    }  
    carry.swap(counter[i]);  
    if (i == fill) ++fill;  
  }  
  for(int i = 1; i < fill; ++i)  
    counter[i].merge(counter[i-1]);  
  swap(counter[fill-1]);  
}  
```
