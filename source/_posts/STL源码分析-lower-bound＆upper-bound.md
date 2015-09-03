title: STL源码分析-lower_bound＆upper_bound
date: 2015-02-15 19:59:47
categories: STL源码分析
tags: 二分
---
本文详细分析下STL库中binary search 的实现。
<!--more-->

```cpp
ForwardIterator
lower_bound (ForwardIterator beg, ForwardIterator end, const T& value)
ForwardIterator
upper_bound (ForwardIterator beg, ForwardIterator end, const T& value)
//搜寻第一个或者最后一个可能位置。
```
具体是什么意思呢？
1. lower_bound 指的是 返回第一个 ”大于等于 value“ 的元素位置。
    另一种解释是 可插入”元素值为 value“且 ”不破坏有序性“的第一个位置
2. upper_bound 指的是 返回第一个 “大于 value ” 的元素位置；
    另一种解释是 可插入”元素值为 value“且 ”不破坏有序性“的最后一个位置

举个例子： 1 2 2 3 4 5, value = 2:
lower_bound 返回的位置是 第 1 个位置；  
upper_bound 返回的位置是 第 3 个位置。

大家可以看到一个很有意思的性质：  
*upper_bound - lower_bound = 数组中 value 的个数*

下面我们来研究下 lower_bound 和 upper_bound 的实现。
以下是我按照源码改写的一个版本：
```cpp
// return the first element which >= x  
int LowerBound(int *a, int n, int x)  
{  
  int left(0), right(n-1), mid;  

  while(left <= right){  
    mid = left + ((right - left) >> 1);  
    if(a[mid] < x){  
        left = mid + 1;  
    }  
    else{  
        right = mid - 1;  
    }  
  }  

  return left;  
}  

// return first element which > x  
int UpperBound(int *a, int n, int x)  
{  
  int left(0), right(n-1), mid;  

  while(left <= right){  
      mid = left + ((right - left) >> 1);  
      if(a[mid] <= x){  
          left = mid + 1;  
      }  
      else{  
          right = mid - 1;  
      }  
  }  

  return left;  
}  
```


大家知道，在 STL 库中，区间一般采用半闭半开区间 [begin, last)，
这种做法的好处是 last - begin 就是区间元素的个数。
以下是 STL 中的源码：
```cpp
template <class _ForwardIter, class _Tp, class _Distance>  
_ForwardIter __lower_bound(_ForwardIter __first, _ForwardIter __last,  
                           const _Tp& __val, _Distance*)
{  
  _Distance __len = 0;  
  distance(__first, __last, __len);  
  _Distance __half;  
  _ForwardIter __middle;  

  while (__len > 0) {  
    __half = __len >> 1;  
    __middle = __first;  
    advance(__middle, __half);  
    if (*__middle < __val) {  
      __first = __middle;  
      ++__first;  
      __len = __len - __half - 1;  
    }  
    else  
      __len = __half;  
  }  
  return __first;  
}  
```

###lower_bound的一个应用：
最长递增子序列问题：分析见  http://blog.csdn.net/shoulinjun/article/details/13775177
代码如下：
```cpp
int LIS(int *a, int n)  
{  
    if(n <= 0) return 0;  
    vector<int> maxV;  
    maxV.push_back(a[0]);  
    for(int i=0; i<n; ++i)  
    {  
        if(a[i] > *maxV.rbegin())  
            maxV.push_back(a[i]);  
        else  
            *lower_bound(maxV.begin(), maxV.end(), a[i]) = a[i];  
    }  
    return maxV.size();  
}  
```
