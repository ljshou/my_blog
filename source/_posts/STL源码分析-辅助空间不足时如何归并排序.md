title: STL源码分析---辅助空间不足时如何归并排序
date: 2015-02-09 21:10:47
categories: STL源码分析
tags: 
---
分析STL库中的implace_merge函数
<!--more-->

两个连在一起的序列 [first, middle) 和 [middle, last) 都已经排序，
归并排序最核心的算法就是 将 [first, middle) 和 [middle, last) 在 O(N)时间内合并成一个有序数组。

但是合并的过程中一般需要  m + n 的额外辅助空间。其中， m 、 n 是数组的左右半边的长度。
现在假如，
+ 辅助空间 bufSize < m + n 呢， 但是比 min(m, n) 大。也就是说能够容纳序列1 或者 序列 2。
+ bufSize < min(m, n) 呢？？
+ 假如没有辅助内存呢？？

STL implace_merge 函数在其实现中分别考虑了上述三种情况，并且尽可能地使效率比较高。
记号：长度为 m 的有序数组 A， 长度为 n 的有序数组 B，

####Case 1: buffer 能够容纳其中的一个有序数组
1. 能够容纳 序列 1 [first, middle)
![](http://ljshou.qiniudn.com/merge1.png)
这时候只要先把 [first, middle) 拷贝到 [buffer, end_buffer) 中，
然后进行常规的 Merge, 依次将 [middle, last) 和 [buffer, end_buffer) 中小的元素放到 [first, last) 即可。不会存在数据覆盖的问题。  
STL 源码如下：
```cpp
template <class _BidirectionalIter, class _Distance, class _Pointer>  
void __merge_adaptive(_BidirectionalIter __first,  
                      _BidirectionalIter __middle,
                      _BidirectionalIter __last,  
                      _Distance __len1, _Distance __len2,  
                      _Pointer __buffer, _Distance __buffer_size) {  
  if (__len1 <= __len2 && __len1 <= __buffer_size) {  
    _Pointer __buffer_end = copy(__first, __middle, __buffer);  
    merge(__buffer, __buffer_end, __middle, __last, __first);  
  }  
```
copy(first, _middle, _buffer) 就是将 [first, middle) 复制到 buffer 中，
然后调用 STL 库的merge。

2. 能够容纳 序列 2 [middle，last)
![](http://ljshou.qiniudn.com/merge2.png)
这时候将 [middle, last) 复制到 [buffer, end_buffer) 中，然后 逆向 merge 即可。
从两个序列的尾部向前，依次将大的元素放到数组的尾部。。
```cpp
else if (__len2 <= __buffer_size) {  
  _Pointer __buffer_end = copy(__middle, __last, __buffer);  
  __merge_backward(__first, __middle, __buffer, __buffer_end, __last);  
}  
```

```cpp
template <class _BidirectionalIter1, class _BidirectionalIter2,  
          class _BidirectionalIter3, class _Compare>  
_BidirectionalIter3 __merge_backward(_BidirectionalIter1 __first1,  
                                     _BidirectionalIter1 __last1,  
                                     _BidirectionalIter2 __first2,  
                                     _BidirectionalIter2 __last2,  
                                     _BidirectionalIter3 __result,  
                                     _Compare __comp) {  
  if (__first1 == __last1)  
    return copy_backward(__first2, __last2, __result);  
  if (__first2 == __last2)  
    return copy_backward(__first1, __last1, __result);  
  --__last1;  
  --__last2;  
  while (true) {  
    if (__comp(*__last2, *__last1)) {  
      *--__result = *__last1;  
      if (__first1 == __last1)  
        return copy_backward(__first2, ++__last2, __result);  
      --__last1;  
    }  
    else {  
      *--__result = *__last2;  
      if (__first2 == __last2)  
        return copy_backward(__first1, ++__last1, __result);  
      --__last2;  
    }  
  }  
}  
```

###Case 2: buffer 大小不足以容纳 [first, middle) 和 [middle，last)

上面的两种思路其实挺巧的，但是现在buffer 更小，怎么办？？  

采取分治的思想，将问题的规模降下来，递归调用子问题，直到对于子问题，这个Buffer 大小足以容纳某一个有序序列。

我们的思路是：将数组分成  【1】  【2】  【3】  【4】 四个小数组。
                         交换 [2]  [3]，使得 【1 3】 作为新的子问题， 递归调用；
                                                       【2 4】作为新的子问题， 递归调用；
注意： 要使得最终数组有序，必须满足 【1 3】 中所有元素 <= 【2 4】中所有元素（这就是我们切数组时要满足的要求）
具体来说，
假如 [first, middle) 长度小于 [middle, last)。

STEP 1：
    我们拿长的序列开刀，对半开。

![](http://ljshou.qiniudn.com/merge3.png)

STEP 2:
   在对 [middle, first) 切时，要满足 数组 【3】 中元素 <= 数组【2】中的元素，
                                                                【2】中元素 <= 数组【4】中的元素
    Bingo, 其实只要在 [middle, last) 中 二分搜索【1】中最后一个元素 5。  ![](http://ljshou.qiniudn.com/merge4.png)
STEP 3:
   将数组 【2】和数组【3】rotate 即可。

   ![](http://ljshou.qiniudn.com/merge5.png)

STEP 4：
  递归调用 【1】【3】， 和 【2】【4】；
直到 bufferSize >= 序列1或者序列2.

完整代码如下：
```cpp
template <class _BidirectionalIter, class _Distance, class _Pointer>  
void __merge_adaptive(_BidirectionalIter __first,  
                      _BidirectionalIter __middle,
                      _BidirectionalIter __last,  
                      _Distance __len1, _Distance __len2,  
                      _Pointer __buffer, _Distance __buffer_size) {  
  if (__len1 <= __len2 && __len1 <= __buffer_size) {  
    _Pointer __buffer_end = copy(__first, __middle, __buffer);  
    merge(__buffer, __buffer_end, __middle, __last, __first);  
  }  
  else if (__len2 <= __buffer_size) {  
    _Pointer __buffer_end = copy(__middle, __last, __buffer);  
    __merge_backward(__first, __middle, __buffer, __buffer_end, __last);  
  }  
  else {  
    _BidirectionalIter __first_cut = __first;  
    _BidirectionalIter __second_cut = __middle;  
    _Distance __len11 = 0;  
    _Distance __len22 = 0;  
    if (__len1 > __len2) {  
      __len11 = __len1 / 2;  
      advance(__first_cut, __len11);  
      __second_cut = lower_bound(__middle, __last, *__first_cut);  
      distance(__middle, __second_cut, __len22);
    }  
    else {  
      __len22 = __len2 / 2;  
      advance(__second_cut, __len22);  
      __first_cut = upper_bound(__first, __middle, *__second_cut);  
      distance(__first, __first_cut, __len11);  
    }  
    _BidirectionalIter __new_middle =  
      __rotate_adaptive(__first_cut, __middle, __second_cut, __len1 - __len11,  
                        __len22, __buffer, __buffer_size);  
    __merge_adaptive(__first, __first_cut, __new_middle, __len11,  
                     __len22, __buffer, __buffer_size);  
    __merge_adaptive(__new_middle, __second_cut, __last, __len1 - __len11,  
                     __len2 - __len22, __buffer, __buffer_size);  
  }  
}  
```


####参考资料：
侯捷， 《STL 源码分析》
