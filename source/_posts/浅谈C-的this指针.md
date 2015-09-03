title: 浅谈C++的this指针
date: 2014-11-05 14:31:52
categories: cplusplus
tags: cplusplus
---
一个关于C++很坑的问题，能够对this指针能够有更好的认识
<!--more-->
##代码
```
class A {
public:
    A(int x=0): a(x) {}
    void print() {
        cout << "print ok" << endl;
    }
    void print_int() {
        cout << "int = " << a << endl;
    }
private:
    int a;
};
int main(void)
{
    A *p = NULL; //空指针　
    p->print();  //语句A
    p->print_int(); //语句B

    return 0;
}
```

###错误的结论
上述代码是否有问题？
第一眼一看，程序试图通过一个NULL指针，访问成员函数，显然会出现 segmentation fault.

###结果
语句A正确运行，而语句B 出现 segmentation fault.
语句B段错误可以理解，因为试图访问地址0，没有权限，所以段错误。但是为什么第一个print调用成功呢？
难道 －> 不是被编译器理解成(*p)吗？那么试图访问0地址，必然会段错误。

###原因
C++对象模型为了提高空间和时间效率，**static数据成员被放置到对象内部，static数据成员， static and nonstatic 函数成员均被放到对象之外**。  

成员函数是独立于对象，可能会让大家很疑惑。static成员函数可以理解，但是non-static函数呢，如何去访问non-static成员变量呢？其实这是通过指向对象的this指针来实现的。成员函数是否具有指向对象的this指针，这就是non-static, 和static成员函数的区别所在。  

现在也就可以理解上面的这段代码了。print() 和 print_int()都是non-static函数，所以它们具有 this 指针，指向绑定的对象。但是，
第一个语句  ```A *p = NULL;``` 将this指针赋值为NULL。  

+ print()函数内部并没有试图访问成员变量，所以不需要对访问this指针。而函数的地址是独立于对象的，所以可以指针更具指针的类型，找到函数的定义。
+ print_int()内部需要访问成员变量a, 也就是 this->a，因此段错误。

