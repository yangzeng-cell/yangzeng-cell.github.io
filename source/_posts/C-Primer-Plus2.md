---
title: C-Primer-Plus2
date: 2023-03-04 17:25:36
- [c++]
categories:
- [c++]
---

# 函数探幽

## C++内联函数

编译过程的最终产品是可执行程序——由一组机器语言指令组成。 运行程序时，操作系统将这些指令载入到计算机内存中，因此每条指令都有特定的内存地址。计算机随后将逐步执行这些指令。有时（如有循环或分支语句时），将跳过一些指令，向前或向后跳到特定地址。常规函数调用也使程序跳到另一个地址（函数的地址），并在函数结束时返回。下面更详细地介绍这一过程的典型实现。执行到函数调用指令时，程序将在函数调用后立即存储该指令的内存地址，并将函数参数复制到堆栈（为此保留的内存块），跳到标记函数起点的内存单元，执行函数代码（也许还需将返回值放入到寄存器中），然后跳回到地址被保存的指令处（这与阅读文章时停下来看脚注，并在阅读完脚注后返回到以前阅读的地方类似）。来回跳跃并记录跳跃位置意味着以前使用函数时，需要一定的开销。 

C++内联函数提供了另一种选择。内联函数的编译代码与其他程序代码“内联”起来了。也就是说，编译器将使用相应的函数代码替换函数 调用。对于内联代码，程序无需跳到另一个位置处执行代码，再跳回来。因此，内联函数的运行速度比常规函数稍快，但代价是需要占用更多内存。如果程序在10个不同的地方调用同一个内联函数，则该程序将包含该函数代码的10个副本

应有选择地使用内联函数。如果执行函数代码的时间比处理函数调用机制的时间长，则节省的时间将只占整个过程的很小一部分。如果代 码执行时间很短，则内联调用就可以节省非内联调用使用的大部分时间。另一方面，由于这个过程相当快，因此尽管节省了该过程的大部分时间，但节省的时间绝对值并不大，除非该函数经常被调用。

<img src="https://raw.githubusercontent.com/yangzeng-cell/blogimage2/master/%E6%88%AA%E5%B1%8F2023-03-04%2020.41.35.png" style="zoom:50%;" />

要使用这项特性，必须采取下述措施之一：

- 在函数声明前加上关键字inline； 
- 在函数定义前加上关键字inline。

通常的做法是省略原型，将整个定义（即函数头和所有函数代码）放在本应提供原型的地方。

程序员请求将函数作为内联函数时，编译器并不一定会满足这种要求。它可能认为该函数过大或注意到函数调用了自己（内联函数不能递 归），因此不将其作为内联函数；而有些编译器没有启用或实现这种特性。

```cpp
// inline.cpp -- using an inline function
#include <iostream>

// an inline function definition
inline double square(double x) { return x * x; }
int main()
{
    using namespace std;
    double a, b;
    double c = 13.0;

    a = square(5.0);
    b = square(4.5 + 7.5);   // can pass expressions
    cout << "a = " << a << ", b = " << b << "\n";
    cout << "c = " << c;
    cout << ", c squared = " << square(c++) << "\n";
    cout << "Now c = " << c << "\n";
    // cin.get();
    return 0;
}
```

## 引用变量

C++新增了一种复合类型——引用变量。引用是已定义的变量的别 名（另一个名称）引用变量的主要用途是用作函数的形参。通过将引用变量用作参数，函数将使用原始数据，而不是其副本。这样除指针之外，引用也为函数处理大型结构提供了一种非常方便的途径，同时对于设计类来说，引用也是必不可少的。

### 创建引用变量

前面讲过，C和C++使用&符号来指示变量的地址。C++给&符号赋予了另一个含义，将其用来声明引用。例如，要将rodents作为rats变量 的别名，可以这样做： 

```
int rats;
int & rodents = rats //make rodents an alias for rats
```

其中，&不是地址运算符，而是类型标识符的一部分。就像声明中的char*指的是指向char的指针一样，int &指的是指向int的引用。上述引用声明允许将rats和rodents互换——它们指向相同的值和内存单元，

```cpp
// firstref.cpp -- defining and using a reference
#include <iostream>
int main()
{
    using namespace std;
    int rats = 101;
    int & rodents = rats;   // rodents is a reference

    cout << "rats = " << rats;
    cout << ", rodents = " << rodents << endl;
    rodents++;
    cout << "rats = " << rats;
    cout << ", rodents = " << rodents << endl;

// some implementations require type casting the following
// addresses to type unsigned
    cout << "rats address = " << &rats;
    cout << ", rodents address = " << &rodents << endl;
    // cin.get();
    return 0;
}
//rats = 101, rodents = 101
//rats = 102, rodents = 102
//rats address = 0x16faef738, rodents address = 0x16faef738
```

请注意，下述语句中的&运算符不是地址运算符，而是将rodents的类型声明为int &，即指向int变量的引用：  

```
int & rodents = rats;   // rodents is a reference
```

但下述语句中的&运算符是地址运算符，其中&rodents表示rodents引用的变量的地址：

```
cout << ", rodents address = " << &rodents << endl;
```

```
//rats = 101, rodents = 101
//rats = 102, rodents = 102
//rats address = 0x16faef738, rodents address = 0x16faef738
```

从中可知，rats和rodents的值和地址都相同（具体的地址和显示格式随系统而异）。将rodents加1将影响这两个变量。更准确地说， rodents++操作将一个有两个名称的变量加1。

必须在声明引用变量时进行初始化。

引用更接近const指针，必须在创建时进行初始化，一旦与某个变量关联起来，就将一直效忠于它。也就是说：

```
int & rodents  = rats;
```

实际上是下述代码的伪装表示：

```
int * const pr = &rats;
```

其中，引用rodents扮演的角色与表达式*pr相同。

```cpp
// secref.cpp -- defining and using a reference
#include <iostream>
int main()
{
    using namespace std;
    int rats = 101;
    int & rodents = rats;   // rodents is a reference

    cout << "rats = " << rats;
    cout << ", rodents = " << rodents << endl;

    cout << "rats address = " << &rats;
    cout << ", rodents address = " << &rodents << endl;

    int bunnies = 50;
    rodents = bunnies;       // can we change the reference?
    cout << "bunnies = " << bunnies;
    cout << ", rats = " << rats;
    cout << ", rodents = " << rodents << endl;

    cout << "bunnies address = " << &bunnies;
    cout << ", rodents address = " << &rodents << endl;
    // cin.get();
    return 0;
}
rats = 101, rodents = 101
rats address = 0x16d5a7738, rodents address = 0x16d5a7738
bunnies = 50, rats = 50, rodents = 50
bunnies address = 0x16d5a772c, rodents address = 0x16d5a7738
```
