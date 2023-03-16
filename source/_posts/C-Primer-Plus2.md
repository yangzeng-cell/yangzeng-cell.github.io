---
title: C-Primer-Plus2
date: 2023-03-04 17:25:36
tags:
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

最初，rodents引用的是rats，但随后程序试图将rodents作为bunnies的引用：

```
rodents = bunnies;
```

咋一看，这种意图暂时是成功的，因为rodents的值从101变为了50。但仔细研究将发现，rats也变成了50，同时rats和rodents的地址相 同，而该地址与bunnies的地址不同。由于rodents是rats的别名，因此上述赋值语句与下面的语句等效： 

```cpp
rats = bunnies;
```

也就是说，这意味着“将bunnies变量的值赋给rat变量”。简而言之，可以通过初始化声明来设置引用，但不能通过赋值来设置。 

```cpp
int rats = 101;
int *pt =&rats;
int & rodents = *pt;
int bunnies = 50;
pt = &bunnies;
```

将rodents初始化为*pt使得rodents指向rats。接下来将pt改为指向bunnies，并不能改变这样的事实，即rodents引用的是rats。 

### 将引用用作函数参数

引用经常被用作函数参数，使得函数中的变量名成为调用程序中的变量的别名。这种传递参数的方法称为按引用传递。按引用传递允许被 调用的函数能够访问调用函数中的变量。C++新增的这项特性是对C语言的超越，C语言只能按值传递。按值传递导致被调用函数使用调用程序的值的拷贝（参见图8.2）。当然，C语言也允许避开按值传递的限制，采用按指针传递的方式。

<img src="https://raw.githubusercontent.com/yangzeng-cell/blogimage2/master/%E6%88%AA%E5%B1%8F2023-03-05%2010.01.02.png" style="zoom:50%;" />

现在我们通过一个常见的的计算机问题——交换两个变量的值，对使用引用和使用指针做一下比较。交换函数必须能够修改调用程序中的 变量的值。这意味着按值传递变量将不管用，因为函数将交换原始变量副本的内容，而不是变量本身的内容。但传递引用时，函数将可以使用原始数据。另一种方法是，传递指针来访问原始数据。程序清单8.4演示了这三种方法，其中包括一种不可行的方法，以便您能对这些方法进行比较

```cpp
// swaps.cpp -- swapping with references and with pointers
#include <iostream>
void swapr(int & a, int & b);   // a, b are aliases for ints
void swapp(int * p, int * q);   // p, q are addresses of ints
void swapv(int a, int b);       // a, b are new variables
int main()
{
    using namespace std;
    int wallet1 = 300;
    int wallet2 = 350;

    cout << "wallet1 = $" << wallet1;
    cout << " wallet2 = $" << wallet2 << endl;

    cout << "Using references to swap contents:\n";
    swapr(wallet1, wallet2);   // pass variables
    cout << "wallet1 = $" << wallet1;
    cout << " wallet2 = $" << wallet2 << endl;

    cout << "Using pointers to swap contents again:\n";
    swapp(&wallet1, &wallet2); // pass addresses of variables
    cout << "wallet1 = $" << wallet1;
    cout << " wallet2 = $" << wallet2 << endl;

    cout << "Trying to use passing by value:\n";
    swapv(wallet1, wallet2);   // pass values of variables
    cout << "wallet1 = $" << wallet1;
    cout << " wallet2 = $" << wallet2 << endl;
    // cin.get();
    return 0;
}

void swapr(int & a, int & b)    // use references
{
    int temp;

    temp = a;       // use a, b for values of variables
    a = b;
    b = temp;
}

void swapp(int * p, int * q)    // use pointers
{
    int temp;

    temp = *p;      // use *p, *q for values of variables
    *p = *q;
    *q = temp;
}

void swapv(int a, int b)        // try using values
{
    int temp;

    temp = a;      // use a, b for values of variables
    a = b;
    b = temp;
}
//wallet1 = $300 wallet2 = $350
//Using references to swap contents:
//wallet1 = $350 wallet2 = $300
//Using pointers to swap contents again:
//wallet1 = $300 wallet2 = $350
//Trying to use passing by value:
//wallet1 = $300 wallet2 = $350
```

引用和指针方法都成功地交换了两个钱夹（wallet）中的内容，而按值传递的方法没能完成这项任务。

按引用传递（swapr(wallet1, wallet2)）和按值传递（swapv(wallet1, waller2)）看起来相同。只能通过原型或函数定义才能知道swapr( )是按 引用传递的。然而，地址运算符（&）使得按地址传递 （swapp(&wallet1, &wallet2)）一目了然（类型声明int * p表明，p是一 个int指针，因此与p对应的参数应为地址，如&wallet1）。

接下来，比较函数swapr( )（按引用传递）和swapv( )（按值传递）的代码，唯一的外在区别是声明函数参数的方式不同：

```
swapr(wallet1, wallet2);   // pass variables
swapv(wallet1, wallet2);   // pass values of variables
```

当然还有内在区别：在swapr( )中，变量a和b是wallet1和wallet2的别名，所以交换a和b的值相当于交换wallet1和wallet2的值；但在swapv( )中，变量a和b是复制了wallet1和waller2的值的新变量，因此交换a和b的,值并不会影响wallet1和wallet2的值。

最后，比较函数swapr( )（传递引用）和swapp( )（传递指针）。第一个区别是声明函数参数的方式不同：

```
void swapr(int & a, int & b);   // a, b are aliases for ints
void swapp(int * p, int * q);   // p, q are addresses of ints
```

另一个区别是指针版本需要在函数使用p和q的整个过程中使用解除引用运算符*。

前面说过，应在定义引用变量时对其进行初始化。函数调用使用实参初始化形参，因此函数的引用参数被初始化为函数调用传递的实参。 也就是说，下面的函数调用将形参a和b分别初始化为wallet1和wallet2：

```
swapr(wallet1, wallet2);
```

### 引用的属性和特别之处

```cpp
// cubes.cpp -- regular and reference arguments
#include <iostream>
double cube(double a);
double refcube(double &ra);
int main ()
{
    using namespace std;
    double x = 3.0;

    cout << cube(x);
    cout << " = cube of " << x << endl;
    cout << refcube(x);
    cout << " = cube of " << x << endl;
    // cin.get();
    return 0;
}

double cube(double a)
{
    a *= a * a;
    return a;
}

double refcube(double &ra)
{
    ra *= ra * ra;
    return ra;
}
//27 = cube of 3
//27 = cube of 27
```

refcube( )函数修改了main( )中的x值，而cube( )没有，这提醒我们为何通常按值传递。变量a位于cube( )中，它被初始化为x的值，但修改a并不会影响x。但由于refcube( )使用了引用参数，因此修改ra实际上就是修改x。如果程序员的意图是让函数使用传递给它的信息，而不对这些信息进行修改，同时又想使用引用，则应使用常量引用。例如，在这个例子中，应在函数原型和函数头中使用const： 

```
double refcube(const double &ra);
```

如果这样做，当编译器发现代码修改了ra的值时，将生成错误消息

顺便说一句，如果要编写类似于上述示例的函数（即使用基本数值类型），应采用按值传递的方式，而不要采用按引用传递的方式。当数 据比较大（如结构和类）时，引用参数将很有用

按值传递的函数，如程序清单8.5中的函数cube( )，可使用多种类型的实参。例如，下面的调用都是合法的：

```
double z = cube(x+2.0);
z = cube(8.0);
int k = 10;
z = cube(k);
double yo[3] = {2.2,3.3,4.4};
z = cube(yo[2]);
```

如果将与上面类似的参数传递给接受引用参数的函数，将会发现，传递引用的限制更严格。毕竟，如果ra是一个变量的别名，则实参应是 该变量。下面的代码不合理，因为表达式x + 3.0并不是变量：

```
double z = refcube(x+3.0);
```

例如，不能将值赋给该表达式：

```
x+3.0 = 5.0;
```

如果试图使用像refcube(x + 3.0)这样的函数调用，将发生什么情况呢？在现代的C++中，这是错误的，大多数编译器都将指出这一点；

之所以做出这种比较温和的反应是由于早期的C++确实允许将表达式传递给引用变量。有些情况下，仍然是这样做的。这样做的结果如 下：由于x + 3.0不是double类型的变量，因此程序将创建一个临时的无名变量，并将其初始化为表达式x + 3.0的值。然后，ra将成为该临时变量的引用。下面详细讨论这种临时变量，看看什么时候创建它们，什么时候不创建

临时变量、引用参数和**const** 

如果实参与引用参数不匹配，C++将生成临时变量。当前，仅当参数为const引用时，C++才允许这样做，但以前不是这样。下面来看看何 种情况下，C++将生成临时变量，以及为何对const引用的限制是合理的。

首先，什么时候将创建临时变量呢？如果引用参数是const，则编译器将在下面两种情况下生成临时变量：

- 实参的类型正确，但不是左值； 
- 实参的类型不正确，但可以转换为正确的类型。

左值是什么呢？左值参数是可被引用的数据对象，例如，变量、数组元素、结构成员、引用和解除引用的指针都是左值。非左值包括字面 常量（用引号括起的字符串除外，它们由其地址表示）和包含多项的表达式。在C语言中，左值最初指的是可出现在赋值语句左边的实体，但这是引入关键字const之前的情况。现在，常规变量和const变量都可视为左值，因为可通过地址访问它们。但常规变量属于可修改的左值，而const变量属于不可修改的左值。 

回到前面的示例。假设重新定义了refcube( )，使其接受一个常量引用参数：

```
double refcube(const double &ra)
{
    return ra*ra * ra;;
}
```

现在考虑下面的代码： 

```
double side = 3.0;
double * pd = &side;
double & rd = side
long edge = side;
double lens[4] = {2.0,5.0,10.0,12.0};
double c1 = refcube(side);
double c2 = refcube(lens[2]);
double c3 = refcube(rd);
double c4 = refcube(*pd);
double c5 = refcube(edge);
double c6 = refcube(7.0);
double c7 = refcube(side + 10.0);
```

参数side、lens[2]、rd和*pd都是有名称的、double类型的数据对象，因此可以为其创建引用，而不需要临时变量（还记得吗，数组元素 的行为与同类型的变量类似）。然而，edge虽然是变量，类型却不正确，double引用不能指向long。另一方面，参数7.0和side + 10.0的类型都正确，但没有名称，在这些情况下，编译器都将生成一个临时匿名变量，并让ra指向它。这些临时变量只在函数调用期间存在，此后编译器便可以随意将其删除。 

那么为什么对于常量引用，这种行为是可行的，其他情况下却不行的呢？对于程序清单8.4中的函数swapr( )：

```cpp
void swapr(int & a, int & b)    // use references
{
    int temp;

    temp = a;       // use a, b for values of variables
    a = b;
    b = temp;
}
```

如果在早期C++较宽松的规则下，执行下面的操作将发生什么情况呢？

```
long a = 3,b = 5;
swapr(a,b);
```

这里的类型不匹配，因此编译器将创建两个临时int变量，将它们初始化为3和5，然后交换临时变量的内容，而a和b保持不变。 

简而言之，如果接受引用参数的函数的意图是修改作为参数传递的变量，则创建临时变量将阻止这种意图的实现。解决方法是，禁止创建 临时变量，现在的C++标准正是这样做的（然而，在默认情况下，有些编译器仍将发出警告，而不是错误消息，因此如果看到了有关临时变量的警告，请不要忽略）。 

现在来看refcube( )函数。该函数的目的只是使用传递的值，而不是修改它们，因此临时变量不会造成任何不利的影响，反而会使函数在可处理的参数种类方面更通用。因此，如果声明将引用指定为const，C++将在必要时生成临时变量。实际上，对于形参为const引用的C++函数，如果实参不匹配，则其行为类似于按值传递，为确保原始数据不被修改，将使用临时变量来存储值。 

如果函数调用的参数不是左值或与相应的const引用参数的类型不匹配，则C++将创建类型正确的匿名变量，将函数调用的参数的值传递给该匿名变量，并让参数来引用该变量。

将引用参数声明为常量数据的引用的理由有三个： 

- 使用const可以避免无意中修改数据的编程错误； 
- 使用const使函数能够处理const和非const实参，否则将只能接受非const数据； 
- 使用const引用使函数能够正确生成并使用临时变量。

C++11新增了另一种引用——右值引用（rvalue reference）。这种引用可指向右值，是使用&&声明的： 

```
double && rref = std::sqrt(36.00); //not allowed for double &
double j =15.0;
double && jref = 2.0*j + 18.5;
std::cout << rref << '\n';
std::cout << jref << '\n';
```

新增右值引用的主要目的是，让库设计人员能够提供有些操作的更有效实现。

### 将引用用于结构

引用非常适合用于结构和类（C++的用户定义类型）。确实，引入引用主要是为了用于这些类型的，而不是基本的内置类型。 

```cpp
//strc_ref.cpp -- using structure references
#include <iostream>
#include <string>
struct free_throws
{
    std::string name;
    int made;
    int attempts;
    float percent;
};

void display(const free_throws & ft);
void set_pc(free_throws & ft);
free_throws & accumulate(free_throws &target, const free_throws &source);

int main()
{
    free_throws one = {"Ifelsa Branch", 13, 14};
    free_throws two = {"Andor Knott", 10, 16};
    free_throws three = {"Minnie Max", 7, 9};
    free_throws four = {"Whily Looper", 5, 9};
    free_throws five = {"Long Long", 6, 14};
    free_throws team = {"Throwgoods", 0, 0};
    free_throws dup;
    set_pc(one);
    display(one);
    accumulate(team, one);
    display(team);
// use return value as argument
    display(accumulate(team, two));
    accumulate(accumulate(team, three), four);
    display(team);
// use return value in assignment
    dup = accumulate(team,five);
    std::cout << "Displaying team:\n";
    display(team);
    std::cout << "Displaying dup after assignment:\n";
    display(dup);
    set_pc(four);
// ill-advised assignment
    accumulate(dup,five) = four;
    std::cout << "Displaying dup after ill-advised assignment:\n";
    display(dup);
    // std::cin.get();
    return 0;
}

void display(const free_throws & ft)
{
    using std::cout;
    cout << "Name: " << ft.name << '\n';
    cout << "  Made: " << ft.made << '\t';
    cout << "Attempts: " << ft.attempts << '\t';
    cout << "Percent: " << ft.percent << '\n';
}
void set_pc(free_throws & ft)
{
    if (ft.attempts != 0)
        ft.percent = 100.0f *float(ft.made)/float(ft.attempts);
    else
        ft.percent = 0;
}

free_throws & accumulate(free_throws & target, const free_throws & source)
{
    target.attempts += source.attempts;
    target.made += source.made;
    set_pc(target);
    return target;
}
```

该程序首先初始化了多个结构对象。本书前面说过，如果指定的初始值比成员少，余下的成员（这里只有percent）将被设置为零。第一个函数调用如下： 

```
set_pc(one);
```

由于函数set_pc()的形参ft为引用，因此ft指向one，函数set_pc()的代码设置成员one.percent。就这里而言，按值传递不可行，因此这将导致设置的是one的临时拷贝的成员percent。根据前一章介绍的知识，另一种方法是使用指针参数并传递地址，但要复杂些：

```
set_prop(&one)

void set_prop(free_throws * one){
	if(pt->attempts！=0)
		pt->percent = 100.0f*float(pt->made)/float(pt->attempts);
	else
		pt->percent = 0;
}
```

```
display(one)
```

由于display()显示结构的内容，而不修改它，因此这个函数使用了一个const引用参数。就这个函数而言，也可按值传递结构，但与复制原始结构的拷贝相比，使用引用可节省时间和内存。

```
accumulate(team, one);
```

函数accumulate()接收两个结构参数，并将第二个结构的成员attempts和made的数据添加到第一个结构的相应成员中。只修改了第一 个结构，因此第一个参数为引用，而第二个参数为const引用：

```
free_throws & accumulate(free_throws & target, const free_throws & source)
```

返回值呢？当前讨论的函数调用没有使用它；就目前而言，原本可以将返回值声明为void，但请看下述函数调用： 

```
display(accumulate(team, two));
```

首先，将结构对象team作为第一个参数传递给了accumulate()。这意味着在函数accumulate()中，target指向的是 team。函数accumulate()修改team，再返回指向它的引用

```
free_throws & accumulate(free_throws & target, const free_throws & source)
```

如果返回类型被声明为free_throws而不是free_throws &，上述返回语句将返回target（也就是team）的拷贝。但返回类型为引用，这意味着返回的是最初传递给accumulate()的team对象。 

接下来，将accumulate()的返回值作为参数传递给了display()，这意味着将team传递给了display()。display()的参数为引用，这意味着函数display()中的ft指向的是team，因此将显示team的内容。所以，下述代码：

```
 display(accumulate(team, two));
```

与下面的代码等效： 

```
accumulate(team, two)
diaplay(team)
```

上述逻辑也适用于如下语句：

```
accumulate(accumulate(team, three), four);
```

因此，该语句与下面的语句等效： 

```
accumulate(team, three);
accumulate(team, four);
```

接下来，程序使用了一条赋值语句：

```
dup = accumulate(team,five);
```

正如您预期的，这条语句将team中的值复制到dup中。 

最后，程序以独特的方式使用了accumulate()：

```
accumulate(dup,five) = four;
```

这条语句将值赋给函数调用，这是可行的，因为函数的返回值是一个引用。如果函数accumulate()按值返回，这条语句将不能通过编译。 由于返回的是指向dup的引用，因此上述代码与下面的代码等效

```
accumulate(dup,five);
dup = four;
```

其中第二条语句消除了第一条语句所做的工作，因此在原始赋值语句使用accumulate()的方式并不好。 

#### 为何要返回引用

下面更深入地讨论返回引用与传统返回机制的不同之处。传统返回机制与按值传递函数参数类似：计算关键字return后面的表达式，并将 结果返回给调用函数。从概念上说，这个值被复制到一个临时位置，而调用程序将使用这个值。请看下面的代码：

```
double m = sqrt(16.0);
cout << sqrt(25.0);
```

在第一条语句中，值4.0被复制到一个临时位置，然后被复制给m。在第二条语句中，值5.0被复制到一个临时位置，然后被传递给cout（这里理论上的描述，实际上，编译器可能合并某些步骤）。 

现在来看下面的语句：

```
dup = accumulate(team,five);
```

如果accumulate()返回一个结构，而不是指向结构的引用，将把整个结构复制到一个临时位置，再将这个拷贝复制给dup。但在返回值为 引用时，将直接把team复制到dup，其效率更高。 

返回引用的函数实际上是被引用的变量的别名。 

#### 返回引用时需要注意的问题

返回引用时最重要的一点是，应避免返回函数终止时不再存在的内存单元引用。您应避免编写下面这样的代码：

```
const free_throws & clone2(free_throws & ft){
	free_throws newguy;
	newguy = ft;
	return newguy;
}
```

该函数返回一个指向临时变量（newguy）的引用，函数运行完毕后它将不再存在。第9章将讨论各种变量的持续性。同样，也应避免返回 指向临时变量的指针。

为避免这种问题，最简单的方法是，返回一个作为参数传递给函数的引用。作为参数的引用将指向调用函数使用的数据，因此返回的引用 也将指向这些数据。程序清单8.6中的accumulate()正是这样做的。 

另一种方法是用new来分配新的存储空间。前面见过这样的函数，它使用new为字符串分配内存空间，并返回指向该内存空间的指针。下 面是使用引用来完成类似工作的方法：

```
const free_throws & clone(free_throws & ft){
	free_throws * pt;
	*pt = ft;
	return *pt;
}
```

第一条语句创建一个无名的free_throws结构，并让指针pt指向该结构，因此*pt就是该结构。上述代码似乎会返回该结构，但函数声明表 明，该函数实际上将返回这个结构的引用。这样，便可以这样使用该函数：

```
free_throws & jolly = clone(three);
```

这使得jolly成为新结构的引用。这种方法存在一个问题：在不再需要new分配的内存时，应使用delete来释放它们。调用clone( )隐藏了对new的调用，这使得以后很容易忘记使用delete来释放内存。第16章讨论的auto_ptr模板以及C++11新增的unique_ptr可帮助程序员自动完成释放工作。

#### 为何将const用于引用返回类型

```
accumulate(dup,five) = four;
```

其效果如下：首先将five的数据添加到dup中，再使用four的内容覆盖dup的内容。这条语句为何能够通过编译呢？在赋值语句中，左边必须是可修改的左值。也就是说，在赋值表达式中，左边的子表达式必须标识一个可修改的内存块。在这里，函数返回指向dup的引用，它确实标识的是一个这样的内存块，因此这条语句是合法的。 

另一方面，常规（非引用）返回类型是右值——不能通过地址访问的值。这种表达式可出现在赋值语句的右边，但不能出现在左边。其他 右值包括字面值（如10.0）和表达式（如x + y）。显然，获取字面值（如10.0）的地址没有意义，但为何常规函数返回值是右值呢？这是因为这种返回值位于临时内存单元中，运行到下一条语句时，它们可能不再存在。

假设您要使用引用返回值，但又不允许执行像给accumulate()赋值这样的操作，只需将返回类型声明为const引用： 

```
const free_throws & accumlate(free_throws & target,const free_throws & source);
```

现在返回类型为const，是不可修改的左值，因此下面的赋值语句不合法：

```
accumlate(dup,five) = four;//not allowed for const reference return
```

该程序中的其他函数调用又如何呢？返回类型为const引用后，下面的语句仍合法：

```
display(accumulate(team,two));
```

这是因为display()的形参也是const free_throws &类型。但下面的语句不合法，因此accumulate()的第一个形参不是const： 

```
accumulate(accumulate(team,three),four);
```

这影响大吗？就这里而言不大，因为您仍可以这样做：

```
accumulate(team,three);
accumulate(team,four);
```

另外，您仍可以在赋值语句右边使用accumulate()。 

通过省略const，可以编写更简短代码，但其含义也更模糊,通常，应避免在设计中添加模糊的特性，因为模糊特性增加了犯错 的机会。将返回类型声明为const引用，可避免您犯糊涂。然而，有时候省略const确实有道理，第11章将讨论的重载运算符<<就是一个这样的例子

### 将引用用于类对象

将类对象传递给函数时，C++通常的做法是使用引用

```cpp
// strquote.cpp  -- different designs
#include <iostream>
#include <string>
using namespace std;
string version1(const string & s1, const string & s2);
const string & version2(string & s1, const string & s2);  // has side effect
const string & version3(string & s1, const string & s2);  // bad design

int main()
{
    string input;
    string copy;
    string result;

    cout << "Enter a string: ";
    getline(cin, input);
    copy = input;
    cout << "Your string as entered: " << input << endl;
    result = version1(input, "***");
    cout << "Your string enhanced: " << result << endl;
    cout << "Your original string: " << input << endl;

    result = version2(input, "###");
    cout << "Your string enhanced: " << result << endl;
    cout << "Your original string: " << input << endl;

    cout << "Resetting original string.\n";
    input = copy;
    result = version3(input, "@@@");
    cout << "Your string enhanced: " << result << endl;
    cout << "Your original string: " << input << endl;
    // cin.get();
    // cin.get();
    return 0;
}

string version1(const string & s1, const string & s2)
{
    string temp;

    temp = s2 + s1 + s2;
    return temp;
}

const string & version2(string & s1, const string & s2)   // has side effect
{
    s1 = s2 + s1 + s2;
// safe to return reference passed to function
    return s1;
}

const string & version3(string & s1, const string & s2)   // bad design
{
    string temp;

    temp = s2 + s1 + s2;
// unsafe to return reference to local variable
    return temp;
}
```



```
string version1(const string & s1, const string & s2)
{
    string temp;

    temp = s2 + s1 + s2;
    return temp;
}
```

它接受两个string参数，并使用string类的相加功能来创建一个满足要求的新字符串。这两个函数参数都是const引用。如果使用string对象作为参数，最终结果将不变： 

```
string version4(string s1,string s2);
```

在这种情况下，s1和s2将为string对象。使用引用的效率更高，因为函数不需要创建新的string对象，并将原来对象中的数据复制到新对象中。限定符const指出，该函数将使用原来的string对象，但不会修改它。

temp是一个新的string对象，只在函数version1( )中有效，该函数执行完毕后，它将不再存在。因此，返回指向temp的引用不可行，因此该 函数的返回类型为string，这意味着temp的内容将被复制到一个临时存储单元中，然后在main( )中，该存储单元的内容被复制到一个名为result的string中： 

```
result = version1(input, "***");
```

对于函数version1( )，您可能注意到了很有趣的一点：该函数的两个形参（s1和s2）的类型都是const string &，但实参（input和“***”）的类型分别是string和const char *。由于input的类型为string，因此让s1指向它没有任何问题。然而，程序怎么能够接受将char指针赋给string引用呢？

这里有两点需要说明。首先，string类定义了一种char *到string的转换功能，这使得可以 使用C-风格字符串来初始化string对象。其次是本章前面讨论过的类型为const引用的形参的一 个属性。假设实参的类型与引用参数类型不匹配，但可被转换为引用类型，程序将创建一个 正确类型的临时变量，使用转换后的实参值来初始化它，然后传递一个指向该临时变量的引 用。例如，在本章前面，将int实参传递给const double &形参时，就是以这种方式进行处理 的。同样，也可以将实参char *或const char *传递给形参const string &。

这种属性的结果是，如果形参类型为const string &，在调用函数时，使用的实参可以是string对象或C-风格字符串，如用引号括起的字符串字面量、以空字符结尾的char数组或指向char的指针变量。因此，下面的代码是可行的：

```
result = version1(input, "***");
```

函数version2( )不创建临时string对象，而是直接修改原来的string对象：

```
const string & version2(string & s1, const string & s2)   // has side effect
{
    s1 = s2 + s1 + s2;
// safe to return reference passed to function
    return s1;
}
```

该函数可以修改s1，因为不同于s2，s1没有被声明为const。 

由于s1是指向main( )中一个对象（input）的引用，因此将s1最为引用返回是安全的。由于s1是指向input的引用，因此，下面一行代码：

```
 result = version2(input, "###");
```

等价于

```
version2(input, "###");
result = input
```

```cpp
const string & version3(string & s1, const string & s2)   // bad design
{
    string temp;

    temp = s2 + s1 + s2;
// unsafe to return reference to local variable
    return temp;
}
```

它存在一个致命的缺陷：返回一个指向version3( )中声明的变量的引用。这个函数能够通过编译（但编译器会发出警告），但当程序试图 执行该函数时将崩溃。具体地说，问题是由下面的赋值语句引发的：

```
    result = version3(input, "@@@");
```

程序试图引用已经释放的内存。

 

### 对象、继承和引用

继承的另一个特征是，基类引用可以指向派生类对象，而无需进行强制类型转换。这种特征的一个实际结果是，可以定义一个接受基类引 用作为参数的函数，调用该函数时，可以将基类对象作为参数，也可以将派生类对象作为参数。

```cpp
//filefunc.cpp -- function with ostream & parameter
#include <iostream>
#include <fstream>
#include <cstdlib>
using namespace std;

void file_it(ostream & os, double fo, const double fe[],int n);
const int LIMIT = 5;
int main()
{
    ofstream fout;
    const char * fn = "ep-data.txt";
    fout.open(fn);
    if (!fout.is_open())
    {
        cout << "Can't open " << fn << ". Bye.\n";
        exit(EXIT_FAILURE);
    }
    double objective;
    cout << "Enter the focal length of your "
            "telescope objective in mm: ";
    cin >> objective;
    double eps[LIMIT];
    cout << "Enter the focal lengths, in mm, of " << LIMIT
         << " eyepieces:\n";
    for (int i = 0; i < LIMIT; i++)
    {
        cout << "Eyepiece #" << i + 1 << ": ";
        cin >> eps[i];
    }
    file_it(fout, objective, eps, LIMIT);
    file_it(cout, objective, eps, LIMIT);
    cout << "Done\n";
    // cin.get();
    // cin.get();
    return 0;
}

void file_it(ostream & os, double fo, const double fe[],int n)
{
    // save initial formatting state
    ios_base::fmtflags initial;
    initial = os.setf(ios_base::fixed, ios_base::floatfield);
    std::streamsize sz = os.precision(0);
    os << "Focal length of objective: " << fo << " mm\n";
    os.precision(1);
    os.width(12);
    os << "f.l. eyepiece";
    os.width(15);
    os << "magnification" << endl;
    for (int i = 0; i < n; i++)
    {
        os.width(12);
        os << fe[i];
        os.width(15);
        os << int (fo/fe[i] + 0.5) << endl;
    }
    // restore initial formatting state
    os.setf(initial, ios_base::floatfield);
    os.precision(sz);
}
```

对于该程序，最重要的一点是，参数os（其类型为ostream &）可以指向ostream对象（如cout），也可以指向ofstream对象（如fout）。该程序还演示了如何使用ostream类中的格式化方法。方法setf( )让您能够设置各种格式化状态。例如，方法setf(ios_base::fixed)将对象置于使用定点表示法的模式；setf(ios_base::showpoint)将对象置于显示小数点的模式，即使小数部分为零。方法precision( )指定显示多少位小数（假定对象处于定点模式下）。所有这些设置都将一直保持不变，直到再次调用相应的方法重新 设置它们。方法width( )设置下一次输出操作使用的字段宽度，这种设置只在显示下一个值时有效，然后将恢复到默认设置。默认的字段宽度为零，这意味着刚好能容纳下要显示的内容。 

函数file_it( )使用了两个有趣的方法调用：

```cpp
ios_base::fmtflags initial;
    initial = os.setf(ios_base::fixed, ios_base::floatfield);
    std::streamsize sz = os.precision(0);
    os << "Focal length of objective: " << fo << " mm\n";
    os.precision(1);
    os.width(12);
    os << "f.l. eyepiece";
    os.width(15);
    os << "magnification" << endl;
    for (int i = 0; i < n; i++)
    {
        os.width(12);
        os << fe[i];
        os.width(15);
        os << int (fo/fe[i] + 0.5) << endl;
    }
    // restore initial formatting state
    os.setf(initial, ios_base::floatfield);
```

方法setf( )返回调用它之前有效的所有格式化设置。ios_base::fmtflags是存储这种信息所需的数据类型名称。因此，将返回值赋给initial将存储调用file_it( )之前的格式化设置，然后便可以使用变量initial作为参数来调用setf( )，将所有的格式化设置恢复到原来的值。因此，该函数将对象回到传递给file_it( )之前的状态。

需要说明的最后一点是，每个对象都存储了自己的格式化设置。因此，当程序将cout传递给file_it( )时，cout的设置将被修改，然后被恢 复；当程序将fout传递给file_it( )时，fout的设置将被修改，然后被恢复。

### 何时使用引用参数

使用引用参数的主要原因有两个。 

- 程序员能够修改调用函数中的数据对象。 
- 通过传递引用而不是整个数据对象，可以提高程序的运行速度。 

当数据对象较大时（如结构和类对象），第二个原因最重要。这些也是使用指针参数的原因。这是有道理的，因为引用参数实际上是基于 指针的代码的另一个接口。那么，什么时候应使用引用、什么时候应使用指针呢？什么时候应按值传递呢？下面是一些指导原则： 

- 对于使用传递的值而不作修改的函数。
- 如果数据对象很小，如内置数据类型或小型结构，则按值传递。 
- 如果数据对象是数组，则使用指针，因为这是唯一的选择，并将指针声明为指向const的指针。 
- 如果数据对象是较大的结构，则使用const指针或const引用，以提高程序的效率。这样可以节省复制结构所需的时间和空间。 
- 如果数据对象是类对象，则使用const引用。类设计的语义常常要求使用引用，这是C++新增这项特性的主要原因。因此，传递类对象参数的标准方式是按引用传递。 
- 对于修改调用函数中数据的函数：如果数据对象是内置数据类型，则使用指针。如果看到诸如fixit（&x）这样的代码（其中x是int），则很明显，该函数将修改x。如果数据对象是数组，则只能使用指针。 
- 如果数据对象是结构，则使用引用或指针。 
- 如果数据对象是类对象，则使用引用。 

当然，这只是一些指导原则，很可能有充分的理由做出其他的选择。例如，对于基本类型，cin使用引用，因此可以使用cin>>n，而不是 cin >> &n。

## 默认参数

默认参数指的是当函数调用中省略了实参时自动使用的一个值。

如何设置默认值呢？必须通过函数原型。由于编译器通过查看原型来了解函数所使用的参数数目，因此函数原型也必须将可能的默认参数 告知程序。方法是将值赋给原型中的参数。

```
char * left (const char * str ,int n = 1);
```

您希望该函数返回一个新的字符串，因此将其类型设置为char *（指向char的指针）；您希望原始字符串保持不变，因此对第一个参数使用了const限定符；您希望n的默认值为1，因此将这个值赋给n。默认参数值是初始化值，因此上面的原型将n初始化为1。如果省略参数 n，则它的值将为1；否则，传递的值将覆盖1。

对于带参数列表的函数，必须从右向左添加默认值。也就是说，要为某个参数设置默认值，则必须为它右边的所有参数提供默认值： 

```
int harpo(int n,int m = 4,int j = 5);//valid
int chico(int n,int m = 6,int j);//invalid
int groucho(int k = 1,int m =2,int n = 3);//valid
```

实参按从左到右的顺序依次被赋给相应的形参，而不能跳过任何参数。

程序清单8.9使用了默认参数。请注意，只有原型指定了默认值。函数定义与没有默认参数时完全相同。 

```cpp
// left.cpp -- string function with a default argument
#include <iostream>
const int ArSize = 80;
char * left(const char * str, int n = 1);
int main()
{
    using namespace std;
    char sample[ArSize];
    cout << "Enter a string:\n";
    cin.get(sample,ArSize);
    char *ps = left(sample, 4);
    cout << ps << endl;
    delete [] ps;       // free old string
    ps = left(sample);
    cout << ps << endl;
    delete [] ps;       // free new string
    // cin.get();
    // cin.get();
    return 0;
}

// This function returns a pointer to a new string
// consisting of the first n characters in the str string.
char * left(const char * str, int n)
{
    if(n < 0)
        n = 0;
    char * p = new char[n+1];
    int i;
    for (i = 0; i < n && str[i]; i++)
        p[i] = str[i];  // copy characters
    while (i <= n)
        p[i++] = '\0';  // set rest of string to '\0'
    return p;
}
```

该程序使用new创建一个新的字符串，以存储被选择的字符。一种可能出现的尴尬情况是，不合作的用户要求的字符数目可能为负。在这 种情况下，函数将字符计数设置为0，并返回一个空字符串。另一种可能出现的尴尬情况是，不负责任的用户要求的字符数目可能多于字符串包含的字符数，为预防这种情况，函数使用了一个组合测试：

```cpp
i < n && str[i]；
```

i < n测试让循环复制了n个字符后终止。测试的第二部分——表达式str[i]，是要复制的字符的编码。遇到空值字符（其编码为0）后，循 环将结束。这样，while循环将使字符串以空值字符结束，并将余下的空间（如果有的话）设置为空值字符。 

另一种设置新字符串长度的方法是，将n设置为传递的值和字符串长度中较小的一个：

```
int len = strlen(str);
n = (n<len) ? n:len;
char *p = new char[n+1];
```

这将确保new分配的空间不会多于存储字符串所需的空间。如果用户执行像left(“Hi!”, 32767)这样的调用，则这种方法很有用。第一种方 法将把“Hi!”复制到由32767个字符组成的数组中，并将除前3个字符之外的所有字符都设置为空值字符；第二种方法将“Hi!”复制到由4个字符组成的数组中。但由于添加了另外一个函数调用（strlen( )），因此程序将更长，运行速度将降低，同时还必须包含头文件cstring（或 string.h）。C程序员倾向于选择运行速度更快、更简洁的代码，因此需要程序员在正确使用函数方面承担更多责任。然而，C++的传统是更强调可靠性。毕竟，速度较慢但能正常运行的程序，要比运行速度虽快但无法正常运行的程序好。如果调用strlen( )所需的时间很长，则可以让left( )直接确定n和字符串长度哪个小。例如，当m的值等于n或到达字符串结尾时，下面的循环都将终止：

```
int m = 0;
while(m<=n&&str[m]!='\0')
	m++
char * p =new char[m+1];
```

别忘了，在str[m]不是空值字符时，表达式str[m] != '\0'的结果为true，否则为false。由于在&&表达式中，非零值被转换为true，而零被 转换为false，

## 函数重载 

函数重载的关键是函数的参数列表——也称为函数特征标（function signature）。如果两个函数的参数数目和类型相同，同时参数的排列顺序也相同，则它们的特征标相同，而变量名是无关紧要的。C++允许定义名称相同的函数，条件是它们的特征标不同。如果参数数目和/或参数类型不同，则特征标也不同。例如，可以定义一组原型如下的print( )函数：

```
void print(const char * str,int width);
void print(double d,int width);
void print(long l,int width);
void print(int i,int width);
void print(const  char * str);
```

使用被重载的函数时，需要在函数调用中使用正确的参数类型

一些看起来彼此不同的特征标是不能共存的。例如，请看下面的两个原型：

```
double cube(double x);
double cube(double &x);
```

您可能认为可以在此处使用函数重载，因为它们的特征标看起来不同。然而，请从编译器的角度来考虑这个问题。假设有下面这样的代 码：

```
cout << cube(x);
```

参数x与double x原型和double &x原型都匹配，因此编译器无法确定究竟应使用哪个原型。为避免这种混乱，编译器在检查函数特征标 时，将把类型引用和类型本身视为同一个特征标。 

匹配函数时，并不区分const和非const变量。

```
void dribble(char * bits);//overloaded
void dribble(const char *cbits);//overloaded
void dabble(char * bits);//not overloaded
void drivel(const char * bits);//not overloaded
```

dribble( )函数有两个原型，一个用于const指针，另一个用于常规指针，编译器将根据实参是否为const来决定使用哪个原型。dribble( )函数只与带非const参数的调用匹配，而drivel( )函数可以与带const或非const参数的调用匹配。drivel( )和dabble( )之所以在行为上有这种差别，主要是由于将非const值赋给const变量是合法的，但反之则是非法的。 

请记住，是特征标，而不是函数类型使得可以对函数进行重载。例如，下面的两个声明是互斥的： 

```
long gronk(int n,float m);//same signatures
double gronk(int n,float m);//not allowed
```

因此，C++不允许以这种方式重载gronk( )。返回类型可以不同，但特征标也必须不同： 

```
long gronk(int n,float m);//difference signatures
double gronk(float n,float m);//allowed
```

类设计和STL经常使用引用参数，因此知道不同引用类型的重载很有用。请看下面三个原型：

```
void sink(double & r1);
void sank(const double &r2);
void sunk(double && r3);
```

左值引用参数r1与可修改的左值参数（如double变量）匹配；const左值引用参数r2与可修改的左值参数、const左值参数和右值参数（如两个double值的和）匹配；最后，左值引用参数r3与左值匹配。注意到与r1或r3匹配的参数都与r2匹配。这就带来了一个问题：如果重载使用这三种参数的函数，结果将如何？答案是将调用最匹配的版本： 

```
void staff(double & rs);
void staff(const double &rcs);
void stove(double &r1);
void stove(const double & r2);
void stove(double && r3);


double x = 55.5;
const double y = 32.0;
stove(x)//call stove(double &);
stove(y)//call stove(const double &);
stove(x+y)//call stove(double &&);如果没有定义函数stove(double &&)，stove(x+y)将调用函数stove(const double &)
```

### 重载示例 

```cpp
// leftover.cpp -- overloading the left() function
#include <iostream>
unsigned long left(unsigned long num, unsigned ct);
char * left(const char * str, int n = 1);

int main()
{
    using namespace std;
    char * trip = "Hawaii!!";   // test value
    unsigned long n = 12345678; // test value
    int i;
    char * temp;

    for (i = 1; i < 10; i++)
    {
        cout << left(n, i) << endl;
        temp = left(trip,i);
        cout << temp << endl;
        delete [] temp; // point to temporary storage
    }
    // cin.get();
    return 0;

}

// This function returns the first ct digits of the number num.
unsigned long left(unsigned long num, unsigned ct)
{
    unsigned digits = 1;
    unsigned long n = num;

    if (ct == 0 || num == 0)
        return 0;       // return 0 if no digits
    while (n /= 10)
        digits++;
    if (digits > ct)
    {
        ct = digits - ct;
        while (ct--)
            num /= 10;
        return num;         // return left ct digits
    }
    else                // if ct >= number of digits
        return num;     // return the whole number
}

// This function returns a pointer to a new string
// consisting of the first n characters in the str string.
char * left(const char * str, int n)
{
    if(n < 0)
        n = 0;
    char * p = new char[n+1];
    int i;
    for (i = 0; i < n && str[i]; i++)
        p[i] = str[i];  // copy characters
    while (i <= n)
        p[i++] = '\0';  // set rest of string to '\0'
    return p;
}
//1
//H
//12
//Ha
//123
//Haw
//1234
//Hawa
//12345
//Hawai
//123456
//Hawaii
//1234567
//Hawaii!
//12345678
//Hawaii!!
//12345678
//Hawaii!!
```

### 何时使用函数重载

虽然函数重载很吸引人，但也不要滥用。仅当函数基本上执行相同的任务，但使用不同形式的数据时，才应采用函数重载。

 

## 函数模板

现在的C++编译器实现了C++新增的一项特性——函数模板。函数模板是通用的函数描述，也就是说，它们使用泛型来定义函数，其中的 泛型可用具体的类型（如int或double）替换。通过将类型作为参数传递给模板，可使编译器生成该类型的函数。由于模板允许以泛型（而不是具体类型）的方式编写程序，因此有时也被称为通用编程。由于类型是用参数表示的，因此模板特性有时也被称为参数化类型（parameterized types）。

函数模板允许以任意类型的方式来定义函数。例如，可以这样建立一个交换模板：

```cpp
template <typename AnyType>
void Swap<AnyType &a,AnyType &b>{
	AnyType temp;
	temp = a;
	a=b;
	b=temp;
}
```

第一行指出，要建立一个模板，并将类型命名为AnyType。关键字template和typename是必需的，除非可以使用关键字class代替typename。另外，必须使用尖括号。类型名可以任意选择（这里为AnyType），只要遵守C++命名规则即可；许多程序员都使用简单的名称，如T。余下的代码描述了交换两个AnyType值的算法。模板并不创建任何函数，而只是告诉编译器如何定义函数。需要交换int的函数时，编译器将按模板模式创建这样的函数，并用int代替AnyType。同样，需要交换double的函数时，编译器将按模板模式创建这样的函数，并用double代替AnyType。 

在标准C++98添加关键字typename之前，C++使用关键字class来创建模板。也就是说，可以这样编写模板定义： 

```cpp
template <class AnyType>
void Swap<AnyType &a,AnyType &b>{
	AnyType temp;
	temp = a;
	a=b;
	b=temp;
}
```

typename关键字使得参数AnyType表示类型这一点更为明显；然而，有大量代码库是使用关键字class开发的。在这种上下文中，这两个 关键字是等价的

```cpp
// funtemp.cpp -- using a function template
#include <iostream>
// function template prototype
template <typename T>  // or class T
void Swap(T &a, T &b);

int main()
{
    using namespace std;
    int i = 10;
    int j = 20;
    cout << "i, j = " << i << ", " << j << ".\n";
    cout << "Using compiler-generated int swapper:\n";
    Swap(i,j);  // generates void Swap(int &, int &)
    cout << "Now i, j = " << i << ", " << j << ".\n";

    double x = 24.5;
    double y = 81.7;
    cout << "x, y = " << x << ", " << y << ".\n";
    cout << "Using compiler-generated double swapper:\n";
    Swap(x,y);  // generates void Swap(double &, double &)
    cout << "Now x, y = " << x << ", " << y << ".\n";
    // cin.get();
    return 0;
}

// function template definition
template <typename T>  // or class T
void Swap(T &a, T &b)
{
    T temp;   // temp a variable of type T
    temp = a;
    a = b;
    b = temp;
}
//i, j = 10, 20.
//Using compiler-generated int swapper:
//Now i, j = 20, 10.
//x, y = 24.5, 81.7.
//Using compiler-generated double swapper:
//Now x, y = 81.7, 24.5.
```

### 重载的模板

```cpp
// twotemps.cpp -- using overloaded template functions
#include <iostream>
template <typename T>     // original template
void Swap(T &a, T &b);

template <typename T>     // new template
void Swap(T *a, T *b, int n);

void Show(int a[]);
const int Lim = 8;
int main()
{
    using namespace std;
    int i = 10, j = 20;
    cout << "i, j = " << i << ", " << j << ".\n";
    cout << "Using compiler-generated int swapper:\n";
    Swap(i,j);              // matches original template
    cout << "Now i, j = " << i << ", " << j << ".\n";

    int d1[Lim] = {0,7,0,4,1,7,7,6};
    int d2[Lim] = {0,7,2,0,1,9,6,9};
    cout << "Original arrays:\n";
    Show(d1);
    Show(d2);
    Swap(d1,d2,Lim);        // matches new template
    cout << "Swapped arrays:\n";
    Show(d1);
    Show(d2);
    // cin.get();
    return 0;
}

template <typename T>
void Swap(T &a, T &b)
{
    T temp;
    temp = a;
    a = b;
    b = temp;
}

template <typename T>
void Swap(T a[], T b[], int n)
{
    T temp;
    for (int i = 0; i < n; i++)
    {
        temp = a[i];
        a[i] = b[i];
        b[i] = temp;
    }
}

void Show(int a[])
{
    using namespace std;
    cout << a[0] << a[1] << "/";
    cout << a[2] << a[3] << "/";
    for (int i = 4; i < Lim; i++)
        cout << a[i];
    cout << endl;
}
//i, j = 10, 20.
//Using compiler-generated int swapper:
//Now i, j = 20, 10.
//Original arrays:
//07/04/1776
//07/20/1969
//Swapped arrays:
//07/20/1969
//07/04/1776
```

### 模板的局限性 

```
template <class T>
void f(T a,T b)
{
....
}
```

通常，代码假定可执行哪些操作。例如，下面的代码假定定义了赋值，但如果T为数组，这种假设将不成立：

a = b;

同样，下面的语句假设定义了<，但如果T为结构，该假设便不成立：

If(a > b)

另外，为数组名定义了运算符>，但由于数组名为地址，因此它比较的是数组的地址，而这可能不是您希望的。下面的语句假定为类型T 定义了乘法运算符，但如果T为数组、指针或结构，这种假设便不成立：

T c =a*b

总之，编写的模板函数很可能无法处理某些类型。另一方面，有时候通用化是有意义的，但C++语法不允许这样做。

### 显式具体化 

#### 第三代具体化（ISO/ANSI C++标准）

- 对于给定的函数名，可以有非模板函数、模板函数和显式具体化模 板函数以及它们的重载版本。 
- 显式具体化的原型和定义应以template<>打头，并通过名称来指出类型。 
- 具体化优先于常规模板，而非模板函数优先于具体化和常规模板。

```
void Swap(job &,job &);//non template function prototype

//template prototype
template <typename T>
void Swap(T &,T &);

//explicitd specialization for the job type
template <> void Swap<job>(job &,job &);
```

正如前面指出的，如果有多个原型，则编译器在选择原型时，非模板版本优先于显式具体化和模板版本，而显式具体化优先于使用模板生 成的版本。

#### 显式具体化示例

```cpp
// twoswap.cpp -- specialization overrides a template
#include <iostream>
template <typename T>
void Swap(T &a, T &b);

struct job
{
    char name[40];
    double salary;
    int floor;
};

// explicit specialization
template <> void Swap<job>(job &j1, job &j2);
void Show(job &j);

int main()
{
    using namespace std;
    cout.precision(2);
    cout.setf(ios::fixed, ios::floatfield);
    int i = 10, j = 20;
    cout << "i, j = " << i << ", " << j << ".\n";
    cout << "Using compiler-generated int swapper:\n";
    Swap(i,j);    // generates void Swap(int &, int &)
    cout << "Now i, j = " << i << ", " << j << ".\n";

    job sue = {"Susan Yaffee", 73000.60, 7};
    job sidney = {"Sidney Taffee", 78060.72, 9};
    cout << "Before job swapping:\n";
    Show(sue);
    Show(sidney);
    Swap(sue, sidney); // uses void Swap(job &, job &)
    cout << "After job swapping:\n";
    Show(sue);
    Show(sidney);
    // cin.get();
    return 0;
}

template <typename T>
void Swap(T &a, T &b)    // general version
{
    T temp;
    temp = a;
    a = b;
    b = temp;
}

// swaps just the salary and floor fields of a job structure

template <> void Swap<job>(job &j1, job &j2)  // specialization
{
    double t1;
    int t2;
    t1 = j1.salary;
    j1.salary = j2.salary;
    j2.salary = t1;
    t2 = j1.floor;
    j1.floor = j2.floor;
    j2.floor = t2;
}

void Show(job &j)
{
    using namespace std;
    cout << j.name << ": $" << j.salary
         << " on floor " << j.floor << endl;
}
//i, j = 10, 20.
//Using compiler-generated int swapper:
//Now i, j = 20, 10.
//Before job swapping:
//Susan Yaffee: $73000.60 on floor 7
//Sidney Taffee: $78060.72 on floor 9
//After job swapping:
//Susan Yaffee: $78060.72 on floor 9
//Sidney Taffee: $73000.60 on floor 7
```

### 实例化和具体化

为进一步了解模板，必须理解术语实例化和具体化。记住，在代码中包含函数模板本身并不会生成函数定义，它只是一个用于生成函数定 义的方案。编译器使用模板为特定类型生成函数定义时，得到的是模板实例（instantiation）。例如，在程序清单8.13中，函数调用Swap(i, j)导致编译器生成Swap( )的一个实例，该实例使用int类型。模板并非函数定义，但使用int的模板实例是函数定义。这种实例化方式被称为隐式实例化（implicit instantiation），因为编译器之所以知道需要进行定义，是由于程序调用Swap( )函数时提供了int参数。

最初，编译器只能通过隐式实例化，来使用模板生成函数定义，但现在C++还允许显式实例化（explicit instantiation）。这意味着可以直接命令编译器创建特定的实例，如Swap<int>( )。其语法是，声明所需的种类——用<>符号指示类型，并在声明前加上关键字template： 

```
template void swap<int>(int,int);
```

实现了这种特性的编译器看到上述声明后，将使用Swap( )模板生成一个使用int类型的实例。也就是说，该声明的意思是“使用Swap( )模板生成int类型的函数定义。”

与显式实例化不同的是，显式具体化使用下面两个等价的声明之一：

```
template <> void swap<int>(int &,int &);
template <> void swap(int &,int &);
```

区别在于，这些声明的意思是“不要使用Swap( )模板来生成函数定义，而应使用专门为int类型显式地定义的函数定义”。这些原型必须有 自己的函数定义。显式具体化声明在关键字template后包含<>，而显式实例化没有。 

试图在同一个文件（或转换单元）中使用同一种类型的显式实例和显式具体化将出错。

还可通过在程序中使用函数来创建显式实例化

```
template <class T>
T Add(T a,T b)
{
	return a+b;
}
...
int m = 6;
double x = 10.2;
cout <<Add<double>(x,m)<<endl;
```

这里的模板与函数调用Add(x, m)不匹配，因为该模板要求两个函数参数的类型相同。但通过使用Add<double>(x, m)，可强制为double类型 实例化，并将参数m强制转换为double类型，以便与函数Add<double> (double, double)的第二个参数匹配。

```
int m = 5;
double x = 14.3;
Swap<double>(m,x);
```

这将为类型double生成一个显式实例化。不幸的是，这些代码不管用，因为第一个形参的类型为double &，不能指向int变量m。

隐式实例化、显式实例化和显式具体化统称为具体化（specialization）。它们的相同之处在于，它们表示的都是使用具体类型的函数定义，而不是通用描述。 

引入显式实例化后，必须使用新的语法——在声明中使用前缀template和template <>，以区分显式实例化和显式具体化。通常，功能 越多，语法规则也越多。下面的代码片段总结了这些概念：

```
template <class T>
void Swap (T &,T &);

template <>void Swap<job>(job &,job &);
int main(void){
	template void Swap<char>(char &,char &);
	short a,b;
	...
	Swap(a,b);
	job n,m;
	...
	Swap(n,m);
	char g,h;
	...
	Swap(g,h)
}
```

编译器看到char的显式实例化后，将使用模板定义来生成Swap( )的char版本。对于其他Swap( )调用，编译器根据函数调用中实际使用的参数，生成相应的版本。例如，当编译器看到函数调用Swap(a, b)后，将生成Swap( )的short版本，因为两个参数的类型都是short。当编译器看到Swap(n, m)后，将使用为job类型提供的独立定义（显式具体化）。当编译器看到Swap(g, h)后，将使用处理显式实例化时生成的模板具体化。

### 编译器选择使用哪个函数版本 

对于函数重载、函数模板和函数模板重载，C++需要（且有）一个 定义良好的策略，来决定为函数调用使用哪一个函数定义，尤其是有多 个参数时。这个过程称为重载解析（overloading resolution）。详细解释这个策略将需要将近一章的篇幅，因此我们先大致了解一下这个过程是如何进行的。 

第1步：创建候选函数列表。其中包含与被调用函数的名称相同的函数和模板函数。 

第2步：使用候选函数列表创建可行函数列表。这些都是参数数目正确的函数，为此有一个隐式转换序列，其中包括实参类型与相应的形参类型完全匹配的情况。例如，使用float参数的函数调用可以将该参数转换为double，从而与double形参匹配，而模板可以为float生成一个实例。 

第3步：确定是否有最佳的可行函数。如果有，则使用它，否则该函数调用出错。

```
may('B')
```

首先，编译器将寻找候选者，即名称为may( )的函数和函数模板。然后寻找那些可以用一个参数调用的函数。例如，下面的函数符合要 求，因为其名称与被调用的函数相同，且可只给它们传递一个参数：

```
void may(int);//#1
float may(float,float = 3);//#2
void may(char);//#3
char *may(const char *);//#4
char may(const char &)//#5
template<class T> void may(const T &);//#6
template<class T> void may(T *);//#7
```

注意，只考虑特征标，而不考虑返回类型。其中的两个候选函数（#4和#7）不可行，因为整数类型不能被隐式地转换（即没有显式强制 类型转换）为指针类型。剩余的一个模板可用来生成具体化，其中T被替换为char类型。这样剩下5个可行的函数，其中的每一个函数，如果它是声明的唯一一个函数，都可以被使用。

接下来，编译器必须确定哪个可行函数是最佳的。它查看为使函数调用参数与可行的候选函数的参数匹配所需要进行的转换。通常，从最 佳到最差的顺序如下所述。 

1．完全匹配，但常规函数优先于模板。 

2．提升转换（例如，char和shorts自动转换为int，float自动转换为double）。 

3．标准转换（例如，int转换为char，long转换为double）。 

4．用户定义的转换，如类声明中定义的转换。

例如，函数#1优于函数#2，因为char到int的转换是提升转换（参见第3章），而char到float的转换是标准转换（参见第3章）。函数#3、函数#5和函数#6都优于函数#1和#2，因为它们都是完全匹配的。#3和#5优于#6，因为#6函数是模板。这种分析引出了两个问题。什么是完全匹配？如果两个函数（如#3和#5）都完全匹配，将如何办呢？通常，有两个函数完全匹配是一种错误，但这一规则有两个例外。显然，我们需要对这一点做更深入的探讨。 

#### 完全匹配和最佳匹配

进行完全匹配时，C++允许某些“无关紧要的转换”。表8.1列出了这些转换——Type表示任意类型。例如，int实参与int &形参完全匹配。注意，Type可以是char &这样的类型，因此这些规则包括从char &到const char &的转换。Type（argument-list）意味着用作实参的函数名与用作形参的函数指针只要返回类型和参数列表相同，就是匹配的（第7章介绍了函数指针以及为何可以将函数名作为参数传递给接受函数指针的函数）。第9章将介绍关键字volatile。 

完全匹配允许的无关紧要转换

| 从实参              | 到形参                  |
| ------------------- | ----------------------- |
| Type                | Type &                  |
| Type &              | Type                    |
| Type []             | *Type                   |
| Type(argument-list) | Type(*)(argument-list)) |
| Type                | const Type              |
| Type                | Voliate Type            |
| Type *              | const Type              |
| Type *              | Voliate Type *          |

```
struct blot {int a,char b[10]};
blot ink = {25,"spots"};
...
recycle(ink);
```

在这种情况下，下面的原型都是完全匹配的： 

```
void recycle(blot);
void recycle(const blot);
void recycle(blot &);
void recycle(const blot &);
```

正如您预期的，如果有多个匹配的原型，则编译器将无法完成重载解析过程；如果没有最佳的可行函数，则编译器将生成一条错误消息， 该消息可能会使用诸如“ambiguous（二义性）”这样的词语。

然而，有时候，即使两个函数都完全匹配，仍可完成重载解析。首先，指向非const数据的指针和引用优先与非const指针和引用参数匹 配。也就是说，在recycle( )示例中，如果只定义了函数#3和#4是完全匹配的，则将选择#3，因为ink没有被声明为const。然而，const和非const之间的区别只适用于指针和引用指向的数据。也就是说，如果只定义了 #1和#2，则将出现二义性错误

一个完全匹配优于另一个的另一种情况是，其中一个是非模板函数，而另一个不是。在这种情况下，非模板函数将优先于模板函数（包 括显式具体化）。 

如果两个完全匹配的函数都是模板函数，则较具体的模板函数优先。例如，这意味着显式具体化将优于使用模板隐式生成的具体化：

```
struct blot {int a,char b[10]};
template <class Type> void recycle (Type t)//template
template <> void recycle<blot> (blot & t);
...
blot ink = {25,"spots"};
...
recycle(ink);
```

术语“最具体（most specialized）”并不一定意味着显式具体化，而是指编译器推断使用哪种类型时执行的转换最少。例如，请看下面两个模板：

```
template <class Type> void recycle (Type t) //#1
template <class Type> void recycle (Type *t) //#2
```

假设包含这些模板的程序也包含如下代码：

```
struct blot {int a,charb[10]};
blot ink = {25,"spots"};
...
recycle(&ink);
```

recycle(&ink)调用与#1模板匹配，匹配时将Type解释为blot *。recycle（&ink）函数调用也与#2模板匹配，这次Type被解释为ink。因 此将两个隐式实例——recycle<blot *>(blot *)和recycle <blot>(blot *)发送到可行函数池中。 

在这两个模板函数中，recycle<blot *>(blot *)被认为是更具体的，因为在生成过程中，它需要进行的转换更少。也就是说，#2模板已经显式指出，函数参数是指向Type的指针，因此可以直接用blot标识Type；而#1模板将Type作为函数参数，因此Type必须被解释为指向blot的指针。也就是说，在#2模板中，Type已经被具体化为指针，因此说它“更具体”。

用于找出最具体的模板的规则被称为函数模板的部分排序规则（partial ordering rules）。和显式实例一样，这也是C++98新增的特性。 

#### 部分排序规则示例 

```cpp
// tempover.cpp --- template overloading
#include <iostream>

template <typename T>            // template A
void ShowArray(T arr[], int n);

template <typename T>            // template B
void ShowArray(T * arr[], int n);

struct debts
{
    char name[50];
    double amount;
};

int main()
{
    using namespace std;
    int things[6] = {13, 31, 103, 301, 310, 130};
    struct debts mr_E[3] =
            {
                    {"Ima Wolfe", 2400.0},
                    {"Ura Foxe", 1300.0},
                    {"Iby Stout", 1800.0}
            };
    double * pd[3];

// set pointers to the amount members of the structures in mr_E
    for (int i = 0; i < 3; i++)
        pd[i] = &mr_E[i].amount;

    cout << "Listing Mr. E's counts of things:\n";
// things is an array of int
    ShowArray(things, 6);  // uses template A
    cout << "Listing Mr. E's debts:\n";
// pd is an array of pointers to double
    ShowArray(pd, 3);      // uses template B (more specialized)
    // cin.get();
    return 0;
}

template <typename T>
void ShowArray(T arr[], int n)
{
    using namespace std;
    cout << "template A\n";
    for (int i = 0; i < n; i++)
        cout << arr[i] << ' ';
    cout << endl;
}

template <typename T>
void ShowArray(T * arr[], int n)
{
    using namespace std;
    cout << "template B\n";
    for (int i = 0; i < n; i++)
        cout << *arr[i] << ' ';
    cout << endl;
}
//Listing Mr. E's counts of things:
//template A
//13 31 103 301 310 130
//Listing Mr. E's debts:
//template B
//2400 1300 1800
```



```
ShowArray(things, 6);
```

标识符things是一个int数组的名称，因此与下面的模板匹配：

```
template <typename T>            // template A
void ShowArray(T arr[], int n);
```

其中T被替换为int类型。 

```
ShowArray(pd, 3); 
```

其中pd是一个double *数组的名称。这与模板A匹配： 

```
template <typename T>            // template A
void ShowArray(T arr[], int n);
```

其中，T被替换为类型double *。在这种情况下，模板函数将显示pd数组的内容，即3个地址。该函数调用也与模板B匹配：

```
template <typename T>            // template B
void ShowArray(T * arr[], int n);
```

在这里，T被替换为类型double，而函数将显示被解除引用的元素 *arr[i]，即数组内容指向的double值。在这两个模板中，模板B更具体，因为它做了特定的假设——数组内容是指针，因此被使用。 

简而言之，重载解析将寻找最匹配的函数。如果只存在一个这样的函数，则选择它；如果存在多个这样的函数，但其中只有一个是非模板 函数，则选择该函数；如果存在多个适合的函数，且它们都为模板函数，但其中有一个函数比其他函数更具体，则选择该函数。如果有多个同样合适的非模板函数或模板函数，但没有一个函数比其他函数更具体，则函数调用将是不确定的，因此是错误的；当然，如果不存在匹配的函数，则也是错误。 

#### 自己选择

在有些情况下，可通过编写合适的函数调用，引导编译器做出您希 望的选择。请看程序清单8.15，该程序将模板函数定义放在文件开头， 从而无需提供模板原型。与常规函数一样，通过在使用函数前提供模板函数定义，它让它也充当原型。

```cpp
// choices.cpp -- choosing a template
#include <iostream>

template<class T>
T lesser(T a, T b)         // #1
{
    return a < b ? a : b;
}

int lesser (int a, int b)  // #2
{
    a = a < 0 ? -a : a;
    b = b < 0 ? -b : b;
    return a < b ? a : b;
}

int main()
{
    using namespace std;
    int m = 20;
    int n = -30;
    double x = 15.5;
    double y = 25.9;

    cout << lesser(m, n) << endl;       // use #2
    cout << lesser(x, y) << endl;       // use #1 with double
    cout << lesser<>(m, n) << endl;     // use #1 with int
    cout << lesser<int>(x, y)  << endl; // use #1 with int

    // cin.get();
    return 0;
}
```



```
cout << lesser<>(m, n) << endl;     // use #1 with int
```

lesser<>(m, n)中的<>指出，编译器应选择模板函数，而不是非模板函数；编译器注意到实参的类型为int，因此使用int替代T对模板进行实例化

```
 cout << lesser<int>(x, y)  << endl; // use #1 with int
```

这条语句要求进行显式实例化（使用int替代T），将使用显式实例化得到的函数。x和y的值将被强制转换为int，该函数返回一个int值，这 就是程序显示15而不是15.5的原因所在。

#### 多个参数的函数

将有多个参数的函数调用与有多个参数的原型进行匹配时，情况将非常复杂。编译器必须考虑所有参数的匹配情况。如果找到比其他可行 函数都合适的函数，则选择该函数。一个函数要比其他函数都合适，其所有参数的匹配程度都必须不比其他函数差，同时至少有一个参数的匹配程度比其他函数都高。 

### 模板函数的发展

#### 是什么类型

```
template<class T1,class T2>
void ft(T1 x,T2 y)
{
	?type?xpy=x+y;
}
```

xpy应为什么类型呢？由于不知道ft()将如何使用，因此无法预先知道这一点。正确的类型可能是T1、T2或其他类型。例如，T1可能是 double，而T2可能是int，在这种情况下，两个变量的和将为double类型。T1可能是short，而T2可能是int，在这种情况下，两个变量的和为int类型。T1还可能是short，而T2可能是char，在这种情况下，加法运算将导致自动整型提升，因此结果类型为int。另外，结构和类可能重载运算符+，这导致问题更加复杂。因此，在C++98中，没有办法声明xpy的类型。 

#### 关键字**decltype**（**C++11**）

C++11新增的关键字decltype提供了解决方案。可这样使用该关键字：

```
int x;
decltype(x) y; //make y the same type as x
```

给decltype提供的参数可以是表达式，因此在前面的模板函数ft()中，可使用下面的代码： 

```
decltype(x+y) xpy;
xpy = x+y;
```

另一种方法是，将这两条语句合而为一： 

```
decltype(x+y) xpy = x+y;
```

因此，可以这样修复前面的模板函数ft()：

```
template<class T1,class T2>
void ft(T1 x,T2 y)
{
	decltype(x+y) xpy = x+y;
}
```



```
decltype(experession) var;
```

第一步：如果expression是一个没有用括号括起的标识符，则var的类型与该标识符的类型相同，包括const等限定符

```
double x =2.5;
double y =7.9;
double &rx = x;
const double *pd;
decltype(x) w;
decltype(rx) u = y;
decltype(pd) v;
```

第二步：如果expression是一个函数调用，则var的类型与函数的返回类型相同： 

```
long indeed(int);
decltype (indeed(3)) m
```

并不会实际调用函数。编译器通过查看函数的原型来获悉返回类型，而无需实际调用函数

第三步：如果expression是一个左值，则var为指向其类型的引用。这好像意味着前面的w应为引用类型，因为x是一个左值。但别忘了， 这种情况已经在第一步处理过了。要进入第三步，expression不能是未用括号括起的标识符。那么，expression是什么时将进入第三步呢？一 种显而易见的情况是，expression是用括号括起的标识符：

```
double xx =4.4;
decltype ((xx)) r2 = xx;//r2 is double &
decltype(xx) w =xx;
```

顺便说一句，括号并不会改变表达式的值和左值性。例如，下面两条语句等效： 

```
xx = 98.6;
(xx) =98.6;
```

第四步：如果前面的条件都不满足，则var的类型与expression的类型相同：

```
int j = 3;
int &k = j;
int &n = j;
decltype(j+6) il;
decltype(100L) i2;
```

请注意，虽然k和n都是引用，但表达式k+n不是引用；它是两个int的和，因此类型为int。 

如果需要多次声明，可结合使用typedef和decltype：

```
tempalte<class T1,class T2>
void ft(T1 x,T2 y){
	typedef decltype(x+y) xytype;
	xytype xpy = x+y;
	xytype arr[10];
	xytype &rxy =arr[2];
}
```

### 另一种函数声明语法（**C++11**后置返回类型） 

```
template<class T1,class T2>
?typ?gt(T1 x,T2 y)
{
	return x+y
}
```

同样，无法预先知道将x和y相加得到的类型。好像可以将返回类型设置为decltype ( x + y)，但不幸的是，此时还未声明参数x和y，它们不 在作用域内（编译器看不到它们，也无法使用它们）。必须在声明参数后使用decltype。为此，C++新增了一种声明和定义函数的语法。下面使用内置类型来说明这种语法的工作原理。对于下面的原型： 

```
double h(int x,float y);
```

使用新增的语法可编写成这样：

```
auto h(int x ,float y) ->double;
```

这将返回类型移到了参数声明后面。->double被称为后置返回类型（trailing return type）。其中auto是一个占位符，表示后置返回类型提供的类型，这是C++11给auto新增的一种角色。这种语法也可用于函数定义：

```
auto h(int x,float y) -> double
{/* function body */}
```

通过结合使用这种语法和decltype，便可给gt()指定返回类型，如下所示：

```
template<class T1,class T2>
auto gt(T1 x,T2 y){
	...
	return x+y;
	...
}
```

现在，decltype在参数声明后面，因此x和y位于作用域内，可以使用它们。 

## 总结

C++扩展了C语言的函数功能。通过将inline关键字用于函数定义，并在首次调用该函数前提供其函数定义，可以使得C++编译器将该函数 视为内联函数。也就是说，编译器不是让程序跳到独立的代码段，以执行函数，而是用相应的代码替换函数调用。只有在函数很短时才能采用内联方式。 

引用变量是一种伪装指针，它允许为变量创建别名（另一个名称）。引用变量主要被用作处理结构和类对象的函数的参数。通常，被 声明为特定类型引用的标识符只能指向这种类型的数据；然而，如果一个类（如ofstream）是从另一个类（如ostream）派生出来的，则基类引用可以指向派生类对象

C++原型让您能够定义参数的默认值。如果函数调用省略了相应的参数，则程序将使用默认值；如果函数调用提供了参数值，则程序将使 用这个值（而不是默认值）。只能在参数列表中从右到左提供默认参数。因此，如果为某个参数提供了默认值，则必须为该参数右边所有的参数提供默认值。 

函数的特征标是其参数列表。程序员可以定义两个同名函数，只要其特征标不同。这被称为函数多态或函数重载。通常，通过重载函数来 为不同的数据类型提供相同的服务。

函数模板自动完成重载函数的过程。只需使用泛型和具体算法来定义函数，编译器将为程序中使用的特定参数类型生成正确的函数定义。 

# 内存模型和名称空间

C++为在内存中存储数据方面提供了多种选择。可以选择数据保留在内存中的时间长度（存储持续性）以及程序的哪一部分可以访问数据 （作用域和链接）等。可以使用new来动态地分配内存，而定位new运算符提供了这种技术的一种变种。C++名称空间是另一种控制访问权的方式。通常，大型程序都由多个源代码文件组成，这些文件可能共享一些数据

## 单独编译

和C语言一样，C++也允许甚至鼓励程序员将组件函数放在独立的文件中。第1章介绍过，可以单独编译这些文件，然后将它们链接成可 执行的程序。（通常，C++编译器既编译程序，也管理链接器。）如果只修改了一个文件，则可以只重新编译该文件，然后将它与其他文件的编译版本链接。这使得大程序的管理更便捷。另外，大多数C++环境都提供了其他工具来帮助管理。例如，UNIX和Linux系统都具有make程序，可以跟踪程序依赖的文件以及这些文件的最后修改时间。运行make时，如果它检测到上次编译后修改了源文件，make将记住重新构建程序所需的步骤。大多数集成开发环境（包括Embarcadero C++ Builder、Microsoft Visual C++、Apple Xcode和Freescale CodeWarrior）都在Project菜单中提供了类似的工具

下面列出了头文件中常包含的内容。

- 函数原型。 
- 使用#define或const定义的符号常量。 
- 结构声明。 
- 类声明。 
- 模板声明。 
- 内联函数。

```cpp
// coordin.h -- structure templates and function prototypes
// structure templates
#ifndef COORDIN_H_
#define COORDIN_H_

struct polar
{
    double distance;    // distance from origin
    double angle;        // direction from origin
};
struct rect
{
    double x;        // horizontal distance from origin
    double y;        // vertical distance from origin
};

// prototypes
polar rect_to_polar(rect xypos);
void show_polar(polar dapos); 

#endif
```

<img src="https://raw.githubusercontent.com/yangzeng-cell/blogimage2/master/%E6%88%AA%E5%B1%8F2023-03-15%2022.22.37.png" style="zoom:50%;" />

头文件管理

在同一个文件中只能将同一个头文件包含一次。记住这个规则很容易，但很可能在不知情的情况下将头文件包含多次。例如，可能使用包含了另外一个头文件的头文件。有一种标准的C/C++技术可以避免多次包含同一个头文件。它是基于预处理器编译指令#ifndef（即if not defined）的。下面的代码片段意味着仅当以前没有使用预处理器编译指令#define定义名称COORDIN*H*时，才处理#ifndef和#endif之间的语句：

```
#ifndef COORDIN_H_
...
#endif
```

通常，使用#define语句来创建符号常量，如下所示： 

```
#define MAXIMUM 4096
```

但只要将#define用于名称，就足以完成该名称的定义，如下所示： 

```
#define COORDIN_H_
```

```
#ifndef COORDIN_H_
#define COORDIN_H_
...
#endif
```

编译器首次遇到该文件时，名称COORDIN*H*没有定义（我们根据include文件名来选择名称，并加上一些下划线，以创建一个在其他地方不太可能被定义的名称）。在这种情况下，编译器将查看#ifndef和#endif之间的内容（这正是我们希望的），并读取定义COORDIN*H*的一 行。如果在同一个文件中遇到其他包含coordin.h的代码，编译器将知道COORDIN*H*已经被定义了，从而跳到#endfi后面的一行上。注意，这种方法并不能防止编译器将文件包含两次，而只是让它忽略除第一次包含之外的所有内容。大多数标准C和C++头文件都使用这种防护（guarding）方案。否则，可能在一个文件中定义同一个结构两次，这将导致编译错误。 

```cpp
// file1.cpp -- example of a three-file program
#include <iostream>
#include "coordin.h" // structure templates, function prototypes
using namespace std;
int main()
{
    rect rplace;
    polar pplace;

    cout << "Enter the x and y values: ";
    while (cin >> rplace.x >> rplace.y)  // slick use of cin
    {
        pplace = rect_to_polar(rplace);
        show_polar(pplace);
        cout << "Next two numbers (q to quit): ";
    }
    cout << "Bye!\n";
// keep window open in MSVC++
/*
    cin.clear();
    while (cin.get() != '\n')
        continue;
    cin.get();
*/
    return 0; 
}
```

```cpp
// file2.cpp -- contains functions called in file1.cpp
#include <iostream>
#include <cmath>
#include "coordin.h" // structure templates, function prototypes

// convert rectangular to polar coordinates
polar rect_to_polar(rect xypos)
{
    using namespace std;
    polar answer;

    answer.distance =
        sqrt( xypos.x * xypos.x + xypos.y * xypos.y);
    answer.angle = atan2(xypos.y, xypos.x);
    return answer;      // returns a polar structure
}

// show polar coordinates, converting angle to degrees
void show_polar (polar dapos)
{
    using namespace std;
    const double Rad_to_deg = 57.29577951;

    cout << "distance = " << dapos.distance;
    cout << ", angle = " << dapos.angle * Rad_to_deg;
    cout << " degrees\n";
}
```

将这两个源代码文件和新的头文件一起进行编译和链接，将生成一个可执行程序

顺便说一句，虽然我们讨论的是根据文件进行单独编译，但为保持通用性，C++标准使用了术语翻译单元（translation unit），而不是文 件；文件并不是计算机组织信息时的唯一方式。

## 存储持续性、作用域和链接性

C++使用三种（在C++11中是四种）不同的方案来存储数据，这些方案的区别就在于数据保留在内存中的时间。 

- 自动存储持续性：在函数定义中声明的变量（包括函数参数）的存储持续性为自动的。它们在程序开始执行其所属的函数或代码块时 被创建，在执行完函数或代码块时，它们使用的内存被释放。C++有两种存储持续性为自动的变量。 
- 静态存储持续性：在函数定义外定义的变量和使用关键字static定义的变量的存储持续性都为静态。它们在程序整个运行过程中都存在。C++有3种存储持续性为静态的变量。
- 线程存储持续性（C++11）：当前，多核处理器很常见，这些CPU可同时处理多个执行任务。这让程序能够将计算放在可并行处理的不同线程中。如果变量是使用关键字thread_local声明的，则其生命 周期与所属的线程一样长。本书不探讨并行编程。 
- 动态存储持续性：用new运算符分配的内存将一直存在，直到使用delete运算符将其释放或程序结束为止。这种内存的存储持续性为动态，有时被称为自由存储（free store）或堆（heap）

### 作用域和链接 

作用域（scope）描述了名称在文件（翻译单元）的多大范围内可见。例如，函数中定义的变量可在该函数中使用，但不能在其他函数中 使用；而在文件中的函数定义之前定义的变量则可在所有函数中使用。链接性（linkage）描述了名称如何在不同单元间共享。链接性为外部的 名称可在文件间共享，链接性为内部的名称只能由一个文件中的函数共享。自动变量的名称没有链接性，因为它们不能共享。 

C++变量的作用域有多种。作用域为局部的变量只在定义它的代码块中可用。代码块是由花括号括起的一系列语句。例如函数体就是代码 块，但可以在函数体中嵌入其他代码块。作用域为全局（也叫文件作用域）的变量在定义位置到文件结尾之间都可用。自动变量的作用域为局部，静态变量的作用域是全局还是局部取决于它是如何被定义的。在函数原型作用域（function prototype scope）中使用的名称只在包含参数列表的括号内可用（这就是为什么这些名称是什么以及是否出现都不重要的原因）。在类中声明的成员的作用域为整个类（参见第10章）。在名称空间中声明的变量的作用域为整个名称空间（由于名称空间已经引入 到C++语言中，因此全局作用域是名称空间作用域的特例）。 

C++函数的作用域可以是整个类或整个名称空间（包括全局的），但不能是局部的（因为不能在代码块内定义函数，如果函数的作用域为 局部，则只对它自己是可见的，因此不能被其他函数调用。这样的函数将无法运行）。 

不同的C++存储方式是通过存储持续性、作用域和链接性来描述的。

### 自动存储持续性

在默认情况下，在函数中声明的函数参数和变量的存储持续性为自动，作用域为局部，没有链接性。也就是说，如果在main( )中声明了一 个名为texas的变量，并在函数oil( )中也声明了一个名为texas变量，则创建了两个独立的变量——只有在定义它们的函数中才能使用它们。对oil( )中的texas执行的任何操作都不会影响main( )中的texas，反之亦然。另外，当程序开始执行这些变量所属的代码块时，将为其分配内存；当函数结束时，这些变量都将消失（注意，执行到代码块时，将为变量分配内存，但其作用域的起点为其声明位置）。

如果在代码块中定义了变量，则该变量的存在时间和作用域将被限制在该代码块内。例如，假设在main( )的开头定义了一个名为teledeli的变量，然后在main( )中开始一个新的代码块，并其中定义了一个新的变量teledeli，则teledeli在内部代码块和外部代码块中都是可见的，而websight就只在内部代码块中可见，它的作用域是从定义它的位置到该代码块的结尾： 

```
int main (){
	int teledeli = 5;
	{
		int teledeli = 2;
	}
}
```

然而，如果将内部代码块中的变量命名为teledeli，而不是websight，使得有两个同名的变量（一个位于外部代码块中，另一个位 于内部代码块中），情况将如何呢？在这种情况下，程序执行内部代码块中的语句时，将teledeli解释为局部代码块变量。我们说，新的定义隐 藏了（hide）以前的定义，新定义可见，旧定义暂时不可见。在程序离开该代码块时，原来的定义又重新可见

```cpp
// autoscp.cpp -- illustrating scope of automatic variables
#include <iostream>
void oil(int x);
int main()
{
    using namespace std;

    int texas = 31;
    int year = 2011;
    cout << "In main(), texas = " << texas << ", &texas = ";
    cout << &texas << endl;
    cout << "In main(), year = " << year << ", &year = ";
    cout << &year << endl;
    oil(texas);
    cout << "In main(), texas = " << texas << ", &texas = ";
    cout << &texas << endl;
    cout << "In main(), year = " << year << ", &year = ";
    cout << &year << endl;
    // cin.get();
    return 0;
}

void oil(int x)
{
    using namespace std;
    int texas = 5;

    cout << "In oil(), texas = " << texas << ", &texas = ";
    cout << &texas << endl;
    cout << "In oil(), x = " << x << ", &x = ";
    cout << &x << endl;
    {                               // start a block
        int texas = 113;
        cout << "In block, texas = " << texas;
        cout << ", &texas = " << &texas << endl;
        cout << "In block, x = " << x << ", &x = ";
        cout << &x << endl;
    }                               // end a block
    cout << "Post-block texas = " << texas;
    cout << ", &texas = " << &texas << endl;
}
//In main(), texas = 31, &texas = 0x16dd07738
//In main(), year = 2011, &year = 0x16dd07734
//In oil(), texas = 5, &texas = 0x16dd076d8
//In oil(), x = 31, &x = 0x16dd076dc
//In block, texas = 113, &texas = 0x16dd076d4
//In block, x = 31, &x = 0x16dd076dc
//Post-block texas = 5, &texas = 0x16dd076d8
//In main(), texas = 31, &texas = 0x16dd07738
//In main(), year = 2011, &year = 0x16dd07734
```

执行到main( )时，程序为texas和year分配空间，使得这些变量可见。当程序调用oil( )时，这些变量仍留在内存 中，但不可见。为两个新变量（x和texas）分配内存，从而使它们可见。在程序执行到oil( )中的内部代码块时，新的texas将不可见，它被一个更新的定义代替。然而，变量x仍然可见，这是因为该代码块没有定义x变量。当程序流程离开该代码块时，将释放最新的texas使用的内存，而第二个texas再次可见。当oil( )函数结束时，texas和x都将过期，而最初的texas和year再次变得可见。 

#### 自动变量的初始化 

可以使用任何在声明时其值为已知的表达式来初始化自动变量

```
int w;
int x = 5;
```

#### 自动变量和栈

了解典型的C++编译器如何实现自动变量有助于更深入地了解自动变量。由于自动变量的数目随函数的开始和结束而增减，因此程序必须 在运行时对自动变量进行管理。常用的方法是留出一段内存，并将其视为栈，以管理变量的增减。之所以被称为栈，是由于新数据被象征性地放在原有数据的上面（也就是说，在相邻的内存单元中，而不是在同一个内存单元中），当程序使用完后，将其从栈中删除。栈的默认长度取决于实现，但编译器通常提供改变栈长度的选项。程序使用两个指针来跟踪栈，一个指针指向栈底——栈的开始位置，另一个指针指向堆顶 ——下一个可用内存单元。当函数被调用时，其自动变量将被加入到栈中，栈顶指针指向变量后面的下一个可用的内存单元。函数结束时，栈顶指针被重置为函数被调用前的值，从而释放新变量使用的内存。 

栈是LIFO（后进先出）的，即最后加入到栈中的变量首先被弹出。这种设计简化了参数传递。函数调用将其参数的值放在栈顶，然后 重新设置栈顶指针。被调用的函数根据其形参描述来确定每个参数的地址。例如，图9.3表明，函数fib( )被调用时，传递一个2字节的int和一个4字节的long。这些值被加入到栈中。当fib( )开始执行时，它将名称real和tell同这两个值关联起来。当fib( )结束时，栈顶指针重新指向以前的位置。新值没有被删除，但不再被标记，它们所占据的空间将被下一个将值加入到栈中的函数调用所使用（图9.3做了简化，因为函数调用可能传递其他信息，如返回地址）。

![](https://raw.githubusercontent.com/yangzeng-cell/blogimage2/master/%E6%88%AA%E5%B1%8F2023-03-16%2022.30.18.png)

#### 寄存器变量

关键字register最初是由C语言引入的，它建议编译器使用CPU寄存器来存储自动变量：

```
register int_count_fast;
```

这旨在提高访问变量的速度。 

在C++11之前，这个关键字在C++中的用法始终未变，只是随着硬件和编译器变得越来越复杂，这种提示表明变量用得很多，编译器可对 其做特殊处理。在C++11中，这种提示作用也失去了，关键字register只是显式地指出变量是自动的。鉴于关键字register只能用于原本就是自动的变量，使用它的唯一原因是，指出程序员想使用一个自动变量，这个变量的名称可能与外部变量相同。这与auto以前的用途完全相同。然而，保留关键字register的重要原因是，避免使用了该关键字的现有代码非法

### 静态持续变量 