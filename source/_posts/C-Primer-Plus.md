---
title: C++ Primer Plus
date: 2022-12-30 23:19:45
tags:
- [c++]
categories:
- [c++]
---

# 开始学习C++

一个cpp文件常见的部分

- 注释，由前缀//标识。 
- 预处理器编译指令#include。 
- 函数头：int main( )。 
- 编译指令using namespace。 
- 函数体，用{和}括起。 
- 使用C++的cout工具显示消息的语句。 
- 结束main( )函数的return语句。 

## **main( )**函数

```c++
#include <iostream>
#include <vector>
#include <string>

using namespace std;

int main()
{
  vector<string> msg{"Hello", "C++", "World", "from", "VS Code", "and the C++ extension!"};

  for (const string &word : msg)
  {
    cout << word << " ";
  }
  cout << endl;
  
  return 0;
}
```

第一行int main( )叫函数头（function heading），花括号({和})中包 括的部分叫函数体。

main( )中最后一条语句叫做返回语句（return statement），它结束 该函数。

在C++中，不能省略分号。

通常，C++函数可被其他函数激活或调用，函数头描述了函数与调 用它的函数之间的接口。位于函数名前面的部分叫做函数返回类型，它 

描述的是从函数返回给调用它的函数的信息。函数名后括号中的部分叫 做形参列表（argument list）或参数列表（parameter list）；它描述的是 从调用函数传递给被调用的函数的信息。这种通用格式用于main( )时让 人感到有些迷惑，因为通常并不从程序的其他部分调用main( )。

然而，通常，main( )被启动代码调用，而启动代码是由编译器添加 到程序中的，是程序和操作系统（UNIX、Windows 7或其他操作系统） 之间的桥梁。事实上，该函数头描述的是main( )和操作系统之间的接 口

然而，通常，main( )被启动代码调用，而启动代码是由编译器添加 到程序中的，是程序和操作系统（UNIX、Windows 7或其他操作系统） 之间的桥梁。事实上，该函数头描述的是main( )和操作系统之间的接 口

来看一下main( )的接口描述，该接口从int开始。C++函数可以给调 用函数返回一个值，这个值叫做返回值（return value）。在这里，从关 键字int可知，main( )返回一个整数值。接下来，是空括号。通常， C++函数在调用另一个函数时，可以将信息传递给该函数。括号中的函 数头部分描述的就是这种信息。在这里，空括号意味着main( )函数不接 受任何信息，或者main( )不接受任何参数。（main( )不接受任何参数并 不意味着main( )是不讲道理的、发号施令的函数。相反，术语参数 （argument）只是计算机人员用来表示从一个函数传递给另一个函数的 信息）。

简而言之，下面的函数头表明main( )函数可以给调用它的函数返回 一个整数值，且不从调用它的函数那里获得任何信息： 

```c++
int main()
```

很多现有的程序都使用经典C函数头：

```c++
main()
```

在C语言中，省略返回类型相当于说函数的类型为int。然而， C++逐步淘汰了这种用法。 也可以使用下面的变体： 

```c++
int main(void)
```

在括号中使用关键字void明确地指出，函数不接受任何参数。在C++（不是C）中，让括号空着与在括号中使用void等效（在C中，让括 号空着意味着对是否接受参数保持沉默）。有些程序员使用下面的函数头，并省略返回语句：

```c++
void main()
```

这在逻辑上是一致的，因为void返回类型意味着函数不返回任何 值。该变体适用于很多系统，但由于它不是当前标准强制的一个选项， 

因此在有些系统上不能工作。因此，读者应避免使用这种格式，而应使 用C++标准格式，这不需要做太多的工作就能完成。最后ANSI/ISO C++标准对那些抱怨必须在main( )函数最后包含一 条返回语句过于繁琐的人做出了让步。如果编译器到达main( )函数末尾 时没有遇到返回语句，则认为main( )函数以如下语句结尾：

```c++
return 0;
```

这条隐含的返回语句只适用于main( )函数，而不适用于其他函数

### 为什么**main( )**不能使用其他名称

通常，C++程序必须包含一个名为main( )的函数（不是Main( )、 MAIN( )或mane( )。记住，大小写和拼写都要正确）.在运行 C++程序时，通常从main( )函数开始执行。因此，如果没有main( )，程 序将不完整，编译器将指出未定义main( )函数。 

存在一些例外情况。例如，在Windows编程中，可以编写一个动态 链接库（DLL）模块，这是其他Windows程序可以使用的代码。由于 

DLL模块不是独立的程序，因此不需要main( )。用于专用环境的程序— 如机器人中的控制器芯片—可能不需要main( )。有些编程环境提供一个 框架程序，该程序调用一些非标准函数，如_tmain( )。在这种情况下， 有一个隐藏的main( )，它调用_tmain( )。但常规的独立程序都需要main( )，

## **C++**注释

C++注释以双斜杠（//）打头,编译器忽略注释,C++也能够识别C注释`/*注释*/`

## **C++**预处理器和**iostream**文件

```c++
#include <iostream>

using namespace std;
```

可使用其他代码替换第2行，这里使用这行代码旨在简化该程序 （如果编译器不接受这几行代码，则说明它没有遵守标准C++98，

C++和C一样，也使用一个预处理器，该程序在进行主编译之前对 源文件进行处理。有些C++实现使用翻译器程序将C++程 序转换为C程序。虽然翻译器也是一种预处理器，但这里不讨论这种预 处理器，而只讨论这样的预处理器，即它处理名称以#开头的编译指 令）。不必执行任何特殊的操作来调用该预处理器，它会在编译程序时 自动运行。 

```
#include <iostream>
```

该编译指令导致预处理器将iostream文件的内容添加到程序中。这 是一种典型的预处理器操作：在源代码被编译之前，替换或添加文本

这提出了一个问题：为什么要将iostream文件的内容添加到程序中 呢？答案涉及程序与外部世界之间的通信。iostream中的io指的是输入 （进入程序的信息）和输出（从程序中发送出去的信息）。C++的输入输出方案涉及iostream文件中的多个定义。为了使用cout来显示消息，第 一个程序需要这些定义。#include编译指令导致iostream文件的内容随源 代码文件的内容一起被发送给编译器。实际上，iostream文件的内容将 取代程序中的代码行#include <iostream>。原始文件没有被修改，而是将源代码文件和iostream组合成一个复合文件，编译的下一阶段将使用 该文件。 

**使用cin和cout进行输入和输出的程序必须包含文件iostream。** 

## 头文件名 

像iostream这样的文件叫做包含文件（include file）—由于它们被包 含在其他文件中；也叫头文件（header file）—由于它们被包含在文件 起始处。C++编译器自带了很多头文件，每个头文件都支持一组特定的 工具。C语言的传统是，头文件使用扩展名h，将其作为一种通过名称标 识文件类型的简单方式。例如，头文件math.h支持各种C语言数学函 数，但C++的用法变了。现在，对老式C的头文件保留了扩展名 h（C++程序仍可以使用这种文件），而C++头文件则没有扩展名。有些 C头文件被转换为C++头文件，这些文件被重新命名，去掉了扩展名 h（使之成为C++风格的名称），并在文件名称前面加上前缀c（表明来 自C语言）。例如，C++版本的math.h为cmath。有时C头文件的C版本和 C++版本相同，而有时候新版本做了一些修改。对于纯粹的C++头文件 （如iostream）来说，去掉h不只是形式上的变化，没有h的头文件也可 以包含名称空间

| 头文件类型  | 约定            | 示例       | 说明                               |
| ----------- | --------------- | ---------- | ---------------------------------- |
| c++旧时风格 | 以.h结尾        | iostream.h | C++程序可以使用                    |
| C旧式风格   | 以.h结尾        | math.h     | C、C++程序可以使用                 |
| c++新式风格 | 没有扩展名      | iostream   | C++程序可以使用，使用namespace std |
| 转换后的C   | 加上前缀c，没有 | cmath      | C++程序可以使用，可以使用不是C的特 |

## 名称空间

如果使用iostream，而不是iostream.h，则应使用下面的名称空间编 译指令来使iostream中的定义对程序可用：

```c++
using namespace std;
```

## 使用**cout**进行**C++**输出

```c++
cout << "messge";
```

### 控制符**endl**

```c++
cout << endl;
```

endl是一个特殊的C++符号，表示一个重要的概念：重起一行。在 输出流中插入endl将导致屏幕光标移到下一行开头。诸如endl等对于cout 来说有特殊含义的特殊符号被称为控制符（manipulator）。和cout一 样，endl也是在头文件iostream中定义的，且位于名称空间std中。

```
cout << "The God, the";
cout << "Bad ,";
cout << "and the uncle";
cout << endl;
```

输出

The God, the Bad ,and the uncle

同样，每个字符串紧接在前一个字符串的后面。如果要在两个字符 串之间留一个空格，必须将空格包含在字符串中。注意，要尝试上述输 出示例，必须将代码放到完整的程序中，该程序包含一个main( )函数头 以及起始和结束花括号

### 换行符

C++还提供了另一种在输出中指示换行的旧式方法：C语言符号 \n： 

```
cout << "what s next?\n";
```

\n被视为一个字符，名为换行符。

显示字符串时，在字符串中包含换行符，而不是在末尾加上endl， 可减少输入量

另一方面，如果要生成一个空行，则两种方法的输入量相同，但对 大多数人而言，输入endl更为方便

```c++
cout << "\n";
cout << endl;
```

显示用引号括起的字符串时，通常使用换行符\n，在其他情 况下则使用控制符endl。一个差别是，endl确保程序继续运行前刷新输 出（将其立即显示在屏幕上）；而使用“\n”不能提供这样的保证，这意 味着在有些系统中，有时可能在您输入信息后才会出现提示。 

## 赋值语句 

C++（和C）有一项不寻常的特性—可以连 续使用赋值运算符

```c++
  int steinway;
  int balnwin;
  int yamaha;
  steinway = balnwin = yamaha = 88;
```

cout 可以用来输出变量

```
cout << steinway
```

### cin

```c++
#include <iostream>
#include <vector>
#include <string>

using namespace std;

int main()
{
  using namespace std;
  int carrots;
  cout << "how many carrots do you have?";
  cout << "endl";
  cin >> carrots;
  cout << "here are two more";
  carrots = carrots + 2;
  cout << "now you are " << carrots << "carrots" << endl;
  return 0;
}
```

上面的输出表明，从键盘输入的值（12）最终被赋给变量carrots。 下面就是执行这项功能的语句： 

```c++
 cin >> carrots;
```

### 使用**cout**进行拼接

```
 cout << "now you are " << carrots << "carrots" << endl;
```

## 函数

C++函数分两种：有返回值的和没有返回值的。在标准C++函数库 中可以找到这两类函数的例子，您也可以自己创建这两种类型的函数

### 使用有返回值的函数

C++程序应当为程序中使用的每个函数提供原型。

函数原型之于函数就像变量声明之于变量—指出涉及的类型。例 如，C++库将sqrt( )函数定义成将一个（可能）带小数部分的数字（如 

6.25）作为参数，并返回一个相同类型的数字。有些语言将这种数字称 为实数，但是C++将这种类型称为double（将在第3章介绍）。sqrt( )的 函数原型像这样：

```c++
double sqrt(double);   //function prototype
```

第一个double意味着sqrt( )将返回一个double值。括号中的double意 味着sqrt( )需要一个double参数。因此该原型对sqrt( )的描述和下面代码 中使用的函数相同

第一个double意味着sqrt( )将返回一个double值。括号中的double意 

味着sqrt( )需要一个double参数。因此该原型对sqrt( )的描述和下面代码 

中使用的函数相同

```c++
double x;
x=sqrt(x)
```

原型结尾的分号表明它是一条语句，这使得它是一个原型，而不是 函数头。如果省略分号，编译器将把这行代码解释为一个函数头，并要 求接着提供定义该函数的函数体。 

在程序中使用sqrt( )时，也必须提供原型。可以用两种方法来实 现：

在源代码文件中输入函数原型； 

包含头文件cmath（老系统为math.h），其中定义了原型。

第二种方法更好，因为头文件更有可能使原型正确。对于C++库中 的每个函数，都在一个或多个头文件中提供了其原型。请通过手册或在 

线帮助查看函数描述来确定应使用哪个头文件。例如，sqrt( )函数的说 明将指出，应使用cmath头文件。（同样，可能必须使用老式的头文件 math.h，它可用于C和C++程序中。）

不要混淆函数原型和函数定义。可以看出，原型只描述函数接口。 也就是说，它描述的是发送给函数的信息和返回的信息。而定义中包含 了函数的代码，如计算平方根的代码。C和C++将库函数的这两项特性 （原型和定义）分开了。库文件中包含了函数的编译代码，而头文件中 则包含了原型。

应在首次使用函数之前提供其原型。通常的做法是把原型放到 main( )函数定义的前面。程序清单2.4演示了库函数sqrt( )的用法，它通 

过包含cmath文件来提供该函数的原型： 

### 函数变体

有些函数需要多项信息。这些函数使用多个参数，参数间用逗号分 开。例如，数学函数pow( )接受两个参数，返回值为以第一个参数为 

底，第二个参数为指数的幂。该函数的原型如下： 

```c++
double pow(double,double) //prototype of a function with two argument;
```

另外一些函数不接受任何参数。例如，有一个C库（与cstdlib或 stdlib.h头文件相关的库）包含一个rand( )函数，该函数不接受任何参 

数，并返回一个随机整数。该函数的原型如下：

```c++
int rand(void);
```

关键字void明确指出，该函数不接受任何参数。如果省略void，让 括号为空，则C++将其解释为一个不接受任何参数的隐式声明。可以这 

样使用该函数：

```
myGuess=rand()
```

还有一些函数没有返回值。例如，假设编写了一个函数，它按美 元、美分格式显示数字。当向它传递参数23.5时，它将在屏幕上显示 

$23.50。由于这个函数把值发送给屏幕，而不是调用程序，因此不需要 返回值。可以在原型中使用关键字void来指定返回类型，以指出函数没 有返回值： 

```c++
void bucks(double)
```

由于它不返回值，因此不能将该函数调用放在赋值语句或其他表达 式中。相反，应使用一条纯粹的函数调用语句：

在有些语言中，有返回值的函数被称为函数（function）；没有返 回值的函数被称为过程（procedure）或子程序（subroutine）。但C++与 C一样，这两种变体都被称为函数。 

### 用户定义的函数

标准C库提供了140多个预定义的函数。如果其中的函数能满足要 求，则应使用它们。但用户经常需要编写自己的函数，尤其是在设计类 

的时候。无论如何，设计自己的函数很有意思，下面来介绍这一过程。 前面已经使用过好几个用户定义的函数，它们都叫main( )。每个C++程 序都必须有一个main( )函数，用户必须对它进行定义。假设需要添加另一个用户定义的函数。和库函数一样，也可以通过函数名来调用用户定 义的函数。对于库函数，在使用之前必须提供其原型，通常把原型放到 main( )定义之前。但现在您必须提供新函数的源代码。最简单的方法 是，将代码放在main( )的后面

```c++
#include <iostream>
void simon(int); // function prototype for simon()

int main()
{
  using namespace std;
  simon(3);
  cout << "pick an integer";
  int count;
  cin >> count;
  simon(count);
  cout << "done!" << endl;
  return 0;
}

void simon(int n)
{ // define this simon function
  using namespace std;
  cout << "simon touch you toe" << n << " times" << endl;
}
```

#### 有返回值的函数

```c++
#include <iostream>
int stonetolb(int);
int main()
{
  using namespace std;
  int stone;
  cout << "enter the weight in stone ";
  cin >> stone;
  int pounds = stonetolb(stone);
  cout << stone << " stone = ";
  cout << pounds << " ponds." << endl;
  return 0;
}

int stonetolb(int sts)
{
  return 14 * sts;
}
```

函数stonetolb( )短小、简单，但包含了全部的函数特性： 

有函数头和函数体； 

接受一个参数； 

返回一个值； 

需要一个原型。 

#### 在多函数程序中使用**using**编译指令 

```
using namespace std;
```

这是因为每个函数都使用了cout，因此需要能够访问位于名称空间 std中的cout定义。 

可以采用另一种方法让两个函数都能够访问名 称空间std，即将编译指令放在函数的外面，且位于两个函数的前面：

```c++
#include <iostream>
void simon(int); // function prototype for simon()
using namespace std;
int main()
{
  
  simon(3);
  cout << "pick an integer";
  int count;
  cin >> count;
  simon(count);
  cout << "done!" << endl;
  return 0;
}

void simon(int n)
{ // define this simon function
  
  cout << "simon touch you toe" << n << " times" << endl;
}
```

当前通行的理念是，只让需要访问名称空间std的函数访问它是更好 的选择。例如，在程序清单2.6中，只有main( )函数使用cout，因此没有 必要让函数stonetolb( )能够访问名称空间std。因此编译指令using被放在 函数main( )中，使得只有该函数能够访问名称空间std。

总之，让程序能够访问名称空间std的方法有多种，下面是其中的4 种。

- 将using namespace std；放在函数定义之前，让文件中所有的函数 都能够使用名称空间std中所有的元素。 
- 将using namespace std；放在特定的函数定义中，让该函数能够使用名称空间std中的所有元素。
- 在特定的函数中使用类似using std::cout;这样的编译指令，而不是 using namespace std;，让该函数能够使用指定的元素，如cout。
- 完全不使用编译指令using，而在需要使用名称空间std中的元素 时，使用前缀std::，如下所示： 

```c++
std:cout << "i am using cout" << std:endl;
```

## 总结

C++程序由一个或多个被称为函数的模块组成。程序从main( )函数 （全部小写）开始执行，因此该函数必不可少。函数由函数头和函数体 

组成。函数头指出函数的返回值（如果有的话）的类型和函数期望通过 参数传递给它的信息的类型。函数体由一系列位于花括号（{}）中的 C++语句组成。 

有多种类型的C++语句，包括下述6种。

- 声明语句：定义函数中使用的变量的名称和类型。 
- 赋值语句：使用赋值运算符（=）给变量赋值。 
- 消息语句：将消息发送给对象，激发某种行动。 
- 函数调用：执行函数。被调用的函数执行完毕后，程序返回到函数 调用语句后面的语句。 
- 函数原型：声明函数的返回类型、函数接受的参数数量和类型
- 返回语句：将一个值从被调用的函数那里返回到调用函数中

类是用户定义的数据类型规范，它详细描述了如何表示信息以及可 对数据执行的操作。对象是根据类规范创建的实体，就像简单变量是根 据数据类型描述创建的实体一样。

C++提供了两个用于处理输入和输出的预定义对象（cin和cout）， 它们是istream和ostream类的实例，这两个类是在iostream文件中定义 的。为ostream类定义的插入运算符（<<）使得将数据插入到输出流成 为可能；为istream类定义的抽取运算符（\>>）能够从输入流中抽取信 息。cin和cout都是智能对象，能够根据程序上下文自动将信息从一种形 式转换为另一种形式。

C++可以使用大量的C库函数。要使用库函数，应当包含提供该函 数原型的头文件。

# 处理数据

内置的C++类型分两组：基本类型和复合类型

 

## 简单变量

```c++
int braincount;
braincount = 5;
```

这些语句告诉程序，它正在存储整数，并使用名称braincount来表 示该整数的值（这里为5）。实际上，程序将找到一块能够存储整数的 

内存，将该内存单元标记为braincount，并将5复制到该内存单元中；然 后，您可在程序中使用braincount来访问该内存单元。这些语句没有告 诉您，这个值将存储在内存的什么位置，但程序确实记录了这种信息。 实际上，可以使用&运算符来检索braincount的内存地址

### 变量名

C++命名规则:

- 在名称中只能使用字母字符、数字和下划线（_）

- 名称的第一个字符不能是数字

- 区分大写字符与小写字符

- 不能将C++关键字用作名称

- 以两个下划线或下划线和大写字母打头的名称被保留给实现（编译 器及其使用的资源）使用。以一个下划线开头的名称被保留给实 现，用作全局标识符.

- C++对于名称的长度没有限制，名称中所有的字符都有意义，但有些平台有长度限制.

  最后一点使得C++与ANSI C（C99标准）有所区别，后者只保证名 称中的前63个字符有意义（在ANSI C中，前63个字符相同的名称被认 为是相同的，即使第64个字符不同）。 

  

如果想用两个或更多的单词组成一个名称，通常的做法是用下划线 字符将单词分开，如my_onions；或者从第二个单词开始将每个单词的 第一个字母大写，如myEyeTooth。（C程序员倾向于按C语言的方式使 用下划线，而Pascal程序员喜欢采用大写方式。）这两种形式都很容易 将单词区分开，如carDrip和cardRip或boat_sport和boats_port。 

### 整型

整数就是没有小数部分的数字，如2、98、-5286和0。整数有很 多，如果将无限大的整数看作很大，则不可能用有限的计算机内存来表 

示所有的整数。因此，语言只能表示所有整数的一个子集。有些语言只 提供一种整型（一种类型满足所有要求！），而C++则提供好几种，这 样便能够根据程序的具体要求选择最合适的整型。

不同C++整型使用不同的内存量来存储整数。使用的内存量越大， 可以表示的整数值范围也越大。另外，有的类型（符号类型）可表示正 值和负值，而有的类型（无符号类型）不能表示负值。术语宽度 （width）用于描述存储整数时使用的内存量。使用的内存越多，则越 宽。C++的基本整型（按宽度递增的顺序排列）分别是char、short、 int、long和C++11新增的long long，其中每种类型都有符号版本和无符 号版本，因此总共有10种类型可供选择.

#### 整型**short**、**int**、**long**和**long long**

short至少16位(bit)

int至少和short一样长

long至少32位，且·至少和int一样长

long long至少64位，且至少和long一样长

```
位：计算机内存的基本单元是位（bit），用01表示
字节：通常指的是8位的内存单元，字节指的就是描述计算机内 存量的度量单位，1KB等于1024字节，1MB等于1024KB。然而，C++对字节的定义与此不 同。C++字节由至少能够容纳实现的基本字符集的相邻位组成。也就是说，可能取值的数目必 须等于或超过字符数目。在美国，基本字符集通常是ASCII和EBCDIC字符集，它们都可以用8 位来容纳，所以在使用这两种字符集的系统中，C++字节通常包含8位。然而，国际编程可能 需要使用更大的字符集，如Unicode，因此有些实现可能使用16位甚至32位的字节。有些人使 用术语八位组（octet）表示8位字节

```

short是short int的简称

long是long int 的简称

long long 是long long int的简称

这些都是符号类型，说明每种的正负值除符号位都是相等的要知道系统中整数的最大长度，可以在程序中使用C++工具来检查 类型的长度。首先，sizeof运算符返回类型或变量的长度，单位为字节 （运算符是内置的语言元素，对一个或多个数据进行运算，并生成一个 值。例如，加号运算符+将两个值相加）。前面说过，“字节”的含义依 赖于实现，因此在一个系统中，两字节的int可能是16位，而在另一个系 统中可能是32位。其次，头文件climits（在老式实现中为limits.h）中包 含了关于整型限制的信息。具体地说，它定义了表示各种限制的符号名 称。例如，INT_MAX为int的最大取值，CHAR_BIT为字节的位数

```c++
#include <iostream>
#include <climits>

int main()
{
  using namespace std;
  int n_int = INT_MAX;
  short n_short = SHRT_MAX;
  long n_long = LONG_MAX;
  long long n_llong = LLONG_MAX;
//对类型名（如int）使用 sizeof运算符时，应将名称放在括号中；但对变量名（如n_short）使用 该运算符，括号是可选的
  cout << "int is " << sizeof(int) << "bytes" << endl;
  cout << "shorts is " << sizeof n_short << "bytes" << endl;
  cout << "long is " << sizeof n_long << "bytes" << endl;
  cout << "long long is " << sizeof n_llong << "bytes" << endl;

  cout << endl;
  cout << "maxinum value: " << endl;
  cout << "int: " << n_int << endl;
  cout << "short: " << n_short << endl;
  cout << "long: " << n_long << endl;
  cout << "long long: " << n_llong << endl
       << endl;
  cout << "max int value: " << INT_MIN << endl;
  cout << "bits per bytes: " << CHAR_BIT << endl;

  return 0;
}
/**
 * 
int is 4bytes
shorts is 2bytes
long is 8bytes
long long is 8bytes

maxinum value: 
int: 2147483647
short: 32767
long: 9223372036854775807
long long: 9223372036854775807

min int value: -2147483648
bits per bytes: 8
*/
```

##### **climits**中的符号常量

| 符号常量   | 表示                       |
| ---------- | -------------------------- |
| CHAR_BIT   | char的位数                 |
| CHAR_MAX   | char的最大值               |
| CHAR_MIN   | char的最小值               |
| SCHAR_MAX  | signed char的最大值        |
| SCHAR_MIN  | signed char的最小值        |
| UCHAR_MAX  | unsigned char的最大值      |
| SHRT_MAX   | short的最大值              |
| SHRT_MIN   | short的最小值              |
| USHRT_MAX  | unsigned short的最大值     |
| INT_MAX    | int的最大值                |
| INT_MIN    | int的最小值                |
| UNIT_MAX   | unsigned int的最大值       |
| LONG_MAX   | long的最大值               |
| LONG_MIN   | long的最小值               |
| ULONG_MAX  | unsigned long的最大值      |
| LLONG_MAX  | long long的最大值          |
| LLONG_MIN  | long long的最小值          |
| ULLONG_MAX | unsigned long long的最大值 |

##### 符号常量**—**预处理器方式

```c++
//在climit文件中
#define MAX_INT 32767；
```

在C++编译过程中，首先将源代码传递给预处理器。在这里，#define和#include一样，也 是一个预处理器编译指令。该编译指令告诉预处理器：在程序中查找INT_MAX，并将所有的 INT_MAX都替换为32767。因此#define编译指令的工作方式与文本编辑器或字处理器中的全 局搜索并替换命令相似。修改后的程序将在完成这些替换后被编译。预处理器查找独立的标 记（单独的单词），跳过嵌入的单词。也就是说，预处理器不会将PINT_MAXTM替换为 P32767IM。也可以使用#define来定义自己的符号常量。然而，#define编 译指令是C语言遗留下来的。C++有一种更好的创建符号常量的方法（使用关键字const，将在 后面的一节讨论），所以不会经常使用#define。然而，有些头文件，尤其是那些被设计成可 用于C和C++中的头文件，必须使用#define。 

##### 初始化 方式

```c++
int uncles=5;
int aunts=uncles;
int chirs=uncle+aunts+4;
//C++还有另一种C语言没有的初始化 语法：
int wrens(144);
```

**如果不对函数内部定义的变量进行初始化，该变量的值将是不确定的。这意味着该变量的值 将是它被创建之前，相应内存单元保存的值。**

##### **C++11**初始化方式

还有另一种初始化方式，这种方式用于数组和结构，但在C++98 中，也可用于单值变量：

```c++
int hamburgers={24};
```

将大括号初始化器用于单值变量的情形还不多，但C++11标准使得 这种情形更多了。首先，采用这种方式时，可以使用等号（=），也可

以不使用：

```c++
int nums{7};//set nums to 7
int rheas={12};// set rheas to 12;
```

其次，大括号内可以不包含任何东西。在这种情况下，变量将被初 始化为零： 

```
int rocs={};
int psychis{};
```

### 无符号类型

```c++
  unsigned short change;
  unsigned int rovert;
  unsigned quarterback; /*默认是int型*/
  unsigned long gone;
  unsigned long long lang_lang;
```

注意，unsigned本身是unsigned int的缩写。

```c++
#include <iostream>
#define ZERO 0
#include <climits>

int main()
{
  using namespace std;
  short sam = SHRT_MAX;
  unsigned short sue = sam;
  cout << "Sam has " << sam << " dollars and sue has " << sue;
  cout << "dollars depoutd " << endl
       << "and $1 to each account." << endl
       << "now";
  sam = sam + 1;
  sue = sue + 1;
  cout << "Sam has " << sam << "dollars and sue has " << sue;
  cout << " dollarrs deposited.\nPoor sam" << endl;
  sam = ZERO;
  sue = ZERO;
  cout << "Sam has " << sam << " dollars and Sue has" << sue;
  cout << "dollars depoutd " << endl;
  cout << "Take $1 from each account" << endl
       << "now";
  sam = sam - 1;
  sue = sue - 1;//无符号数0-1 会在另外一端-1
  cout << "samhas " << sam << " dollars and Sue has " << sue;
  cout << " dollars deposited" << endl
       << "lucky sue " << endl;

  return 0;
}
/**
Sam has 32767 dollars and sue has 32767dollars depoutd
and $1 to each account.
nowSam has -32768dollars and sue has 32768 dollarrs deposited.
Poor sam
Sam has 0 dollars and Sue has0dollars depoutd
Take $1 from each account
nowsamhas -1 dollars and Sue has 65535 dollars deposited
lucky sue
*/
```

该程序将一个short变量（sam）和一个unsigned short变量（sue）分 别设置为最大的short值，在我们的系统上，是32767。然后，将这些变 量的值都加1。这对于sue来说没有什么问题，因为新值仍比无符号整数 的最大值小得多；但sam的值从32767变成了−32768！同样，对于sam， 将其设置为0并减去1，也不会有问题；但对于无符号变量sue，将其设 置为0并减去后，它变成了65535。可以看出，这些整型变量的行为就像 里程表。如果超越了限制，其值将为范围另一端的取值。C++确保了无符号类型的这种行为；但C++并不保证符号整型超 越限制（上溢和下溢）时不出错，而这正是当前实现中最为常见的行 为。

#### **C++**如何确定常量的类型

```c++
cout << "year = "<< 1492 << "\n"
```

程序将把1492存储为int、long还是其他整型呢？答案是，除非有理 由存储为其他类型（如使用了特殊的后缀来表示特定的类型，或者值太 大，不能存储为int），否则C++将整型常量存储为int类型.

首先来看看后缀。后缀是放在数字常量后面的字母，用于表示类 型。整数后面的l或L后缀表示该整数为long常量，u或U后缀表unsigned int常量，ul（可以采用任何一种顺序，大写小写均可）表示 unsigned long常量（由于小写l看上去像1，因此应使用大写L作后缀）。 例如，在int为16位、long为32位的系统上，数字22022被存储为int，占 16位，数字22022L被存储为long，占32位。同样，22022LU和22022UL 都被存储为unsigned long。C++11提供了用于表示类型long long的后缀ll 和LL，还提供了用于表示类型unsigned long long的后缀ull、Ull、uLL和 ULL。

接下来考察长度。在C++中，对十进制整数采用的规则，与十六进 制和八进制稍微有些不同。对于不带后缀的十进制整数，将使用下面几 种类型中能够存储该数的最小类型来表示：int、long或long long。在int 为16位、long为32位的计算机系统上，20000被表示为int类型，40000被 表示为long类型，3000000000被表示为long long类型。对于不带后缀的 十六进制或八进制整数，将使用下面几种类型中能够存储该数的最小类 型来表示：int、unsigned int long、unsigned long、long long或unsigned long long。在将40000表示为long的计算机系统中，十六进制数 0x9C40（40000）将被表示为unsigned int。这是因为十六进制常用来表 示内存地址，而内存地址是没有符号的，因此，usigned int比long更适合 用来表示16位的地址

### **char**类型：字符和小整数

char类型是专为存 储字符（如字母和数字）而设计的

现在，存储数字对于计算机来说算 不了什么，但存储字母则是另一回事。编程语言通过使用字母的数值编 码解决了这个问题。因此，char类型是另一种整型。它足够长，能够表 示目标计算机系统中的所有基本符号—所有的字母、数字、标点符号 等。实际上，很多系统支持的字符都不超过128个，因此用一个字节就可以表示所有的符号。因此，虽然char最常被用来处理字符，但也可以 将它用做比short更小的整型。

最常用的符号集是ASCII字符集.字符集 中的字符用数值编码（ASCII码）表示

```cpp
#include <iostream>
int main()
{
  using namespace std;
  char ch;
  cout << "enter a character: " << endl;
  cin >> ch;
  cout << "hila ";
  cout << "thank you for the " << ch << " charactor";
  return 0;
}

// enter a character: 
// a
// hila thank you for the a charactor
```

C++对字符用单引号，对字符串使用双引号。

```c++
#include <iostream>
int main()
{
  using namespace std;
  char ch = 'M';
  int i = ch;
  cout << "the ascii code for " << ch << " is " << i << endl;
  cout << "add one to the character code:" << endl;
  ch = ch + 1;
  i = ch;
  cout << "the ascii code for " << ch << " is " << i << endl;
  cout << "displaying char ch using cout.put(ch) ";
  cout.put(ch);
  cout.put('!');
  cout << endl
       << "done" << endl;
  return 0;
}
// the ascii code for M is 77
// add one to the character code:
// the ascii code for N is 78
// displaying char ch using cout.put(ch) N!
// done
```

‘M’表示字符M的数值编码，因此将char变量ch.初始化为‘M’，将把c设置为77。然后，程序将同样的值赋给int变量i， 这样ch和i的值都是77。接下来，cout把ch显示为M，而把i显示为77。由于ch实际上是一个整数，因此可以对它使用整数操作，如加1， 这将把ch的值变为78。然后，程序将i重新设置为新的值（也可以将i加 1）。cout再次将这个值的char版本显示为字符，将int版本显示为数字。C++将字符表示为整数提供了方便，使得操纵字符值很容易。不必 使用笨重的转换函数在字符和ASCII码之间来回转换。 

即使通过键盘输入的数字也被视为字符。请看下面的代码

```
char ch;
cin >> ch;
```

如果您输入5并按回车键，上述代码将读取字符“5”，并将其对应的 字符编码（ASCII编码53）存储到变量ch中。请看下面的代码： 

```
int ch;
cin >> ch;
```

如果您也输入5并按回车键，上述代码将读取字符“5”，将其转换为 相应的数字值5，并存储到变量n中

cout.put( )成员函数提供了另一种显示字符的方法，可以替代<<运 算符。

在 C++的Release 2.0之前，cout将字符变量显示为字符，而将字符常量 （如‘M’和‘N’）显示为数字。问题是，C++的早期版本与C一样，也将 把字符常量存储为int类型。也就是说，‘M’的编码77将被存储在一个16 位或32位的单元中。而char变量一般占8位。下面的语句从常量‘M’中复 制8位（左边的8位）到变量ch中

```
char ch='M';
```

遗憾的是，这意味着对cout来说，‘M’和ch看上去有天壤之别，虽 然它们存储的值相同。因此，下面的语句将打印$字符的ASCII码，而不 

是字符$： 

```
cout << '$'
```

但下面的语句将打印字符$：

```
cout.put('$')
```

在Release 2.0之后，C++将字符常量存储为char类型，而不是int类 型。这意味着cout现在可以正确处理字符常量了。

#### **signed char**和**unsigned char**

与int不同的是，char在默认情况下既不是没有符号，也不是有符 号。是否有符号由C++实现决定，这样编译器开发人员可以最大限度地 将这种类型与硬件属性匹配起来。如果char有某种特定的行为对您来说 非常重要，则可以显式地将类型设置为signed char 或unsigned char：

```
char fodo;
unsigned char bar;
signed char snark;
```

如果将char用作数值类型，则unsigned char和signed char之间的差异 将非常重要。unsigned char类型的表示范围通常为0～255，而signed char 的表示范围为−128到127

#### **wcha_t**

程序需要处理的字符集可能无法用一个8位的字节表示，如日文汉字系统。对于这种情况，C++的处理方式有两种.首先，如果大型字符 集是实现的基本字符集，则编译器厂商可以将char定义为一个16位的字 节或更长的字节。其次，一种实现可以同时支持一个小型基本字符集和 一个较大的扩展字符集。8位char可以表示基本字符集，另一种类型 wchar_t（宽字符类型）可以表示扩展字符集。wchar_t类型是型与另一种整型（底层（underlying）类型）的长度和符号属性相同。对底层类型的选择取决于实现，因此在一个系统中，它可能是unsigned short，而在另一个系统中，则可能是int。

cin和cout将输入和输出看作是char流，因此不适于用来处理wchar_t 类型。iostream头文件的最新版本提供了作用相似的工具—wcin和 wcout，可用于处理wchar_t流。另外，可以通过加上前缀L来指示宽字 符常量和宽字符串。下面的代码将字母P的wchar_t版本存储到变量bob 中，并显示单词tall的wchar_t版本： 

```
wchar_t bob =L'P';
wcout<<L"tall"<<endl;
```

在支持两字节wchar_t的系统中，上述代码将把每个字符存储在一 个两个字节的内存单元中

#### **C++11**新增的类型：**char16_t**和**char32_t**

随着编程人员日益熟悉Unicode，类型wchar_t显然不再能够满足需 求。事实上，在计算机系统上进行字符和字符串编码时，仅使用 Unicode码点并不够。具体地说，进行字符串编码时，如果有特定长度 和符号特征的类型，将很有帮助，而类型wchar_t的长度和符号特征随 实现而已。因此，C++11新增了类型char16_t和char32_t，其中前者是无 符号的，长16位，而后者也是无符号的，但长32位。C++11使用前缀u 表示char16_t字符常量和字符串常量，如u‘C’和u“be good”；并使用前缀 U表示char32_t常量，如U‘R’和U“dirty rat”。类型char16_t与/u00F6形式 的通用字符名匹配，而类型char32_t与/U0000222B形式的通用字符名匹 配。前缀u和U分别指出字符字面值的类型为char16_t和char32_t：

```
char16_t ch1=u'q';
char32_t ch2=U'\U000222B'
```

与wchar_t一样，char16_t和char32_t也都有底层类型—一种内置的 整型，但底层类型可能随系统而已。 

### **bool**类型

ANSI/ISO C++标准添加了一种名叫bool的新类型（对C++来说是新 的）。在计算中，布尔变量的值可以是true或false。C++和C一样，也没有布尔类型。C++将 非零值解释为true，将零解释为false。然而，现在可以使用bool类型来 表示真和假了，它们分别用预定义的字面值true和false表示。也就是 说，可以这样编写语句：

```
bool is_ready=true;
```

字面值true和false都可以通过提升转换为int类型，true被转换为1， 而false被转换为0： 

```
int ans=true;
int promise=false;
```

另外，任何数字值或指针值都可以被隐式转换（即不用显式强制转 换）为bool值。任何非零值都被转换为true，而零被转换为false： 

```
bool start=-100 //true
bool stop=0 //false
```

### **const**限定符

是使用const关键字来修改变量声明和初始化

```
const int Months=12;
```

这样，便可以在程序中使用Months，而不是12了（在程序中，12可 能表示一英尺有多少英寸或一打面包圈是多少个，而名称Months指出了 值12表示的是什么）。常量（如Months）被初始化后，其值就被固定 了，编译器将不允许再修改该常量的值。如果您这样做，g++将指出程 序试图给一个只读变量赋值。关键字const叫做限定符，因为它限定了声 明的含义

一种常见的做法是将名称的首字母大写，以提醒您Months是个常 量。这决不是一种通用约定，但在阅读程序时有助于区分常量和变量。 另一种约定是将整个名称大写，使用#define创建常量时通常使用这种约 定。还有一种约定是以字母k打头，如kmonths。当然，还有其他约定。 很多组织都有特殊的编码约定，要求其程序员遵守

如果以前使用过C语言，您可能觉得前面讨论的#define语句已经足 够完成这样的工作了。但const比#defien好。首先，它能够明确指定类 型。其次，可以使用C++的作用域规则将定义限制在特定的函数或文件 中（作用域规则描述了名称在各种模块中的可知程度，将在第9章讨 论）。第三，可以将const用于更复杂的类型，如第4章将介绍的数组和 结构

### 浮点数

#### 书写浮点数

C++有两种书写浮点数的方式。第一种是使用常用的标准小数点表示法

```
12.34
93900.32
0.0023
8.0
```

第二种表示浮点值的方法叫做E表示法，

```
2.52e+8
8.33E-4
7E5
```

E表示法确保数字以浮点格式存储，即使没有小数点。注意，既可 以使用E也可以使用e，指数可以是正数也可以是负数。然而，数字中不能有空格，因此7.2 E6是非法的

#### 浮点类型

和ANSI C一样，C++也有3种浮点类型：float、double和long double。这些类型是按它们可以表示的有效数位和允许的指数最小范围来描述的。有效位（significant figure）是数字中有意义的位。例如，加利福尼亚的Shasta山脉的高度为14179英尺，该数字使用了5个有效位，指出了最接近的英尺数。然而，将Shasta山脉的高度写成约14000英尺 时，有效位数为2位，因为结果经过四舍五入精确到了千位。在这种情 况下，其余的3位只不过是占位符而已。有效位数不依赖于小数点的位 置。例如，可以将高度写成14.162千英尺。这样仍有5个有效位，因为 这个值精确到了第5位.

事实上，C和C++对于有效位数的要求是，float至少32位，double至 少48位，且不少于float，long double至少和double一样多。这三种类型 的有效位数可以一样多。然而，通常，float为32位，double为64位， long double为80、96或128位。另外，这3种类型的指数范围至少是−37到37。可以从头文件cfloat或float.h中找到系统的限制。（cfloat是C语言 的float.h文件的C++版本。）

```js
#include <iostream>
int main()
{
  using namespace std;
  cout.setf(ios_base::fixed, ios_base::floatfield); // fixed-point
  float tub = 10.0 / 3.0;
  double mint = 10.0 / 3.0;
  const float million = 1.0e6;
  cout << "tub = " << tub;
  cout << ", a million tubs = " << million * tub;
  cout << ", \n and ten million tubs = ";
  cout << 10 * million * tub << endl;
  cout << "min t= " << mint << " and a minillion mints = ";
  cout << million * mint << endl;
  return 0;
}

// tub = 3.333333, a million tubs = 3333333.250000,
//  and ten million tubs = 33333332.000000
// min t= 3.333333 and a minillion mints = 3333333.333333
```

通常cout会删除结尾的零。例如，将3333333.250000显示为 3333333.25。调用cout.setf( )将覆盖这种行为，至少在新的实现中是这样 的。这里要注意的是，为何float的精度比double低。tub和mint都被初始 化为10.0/3.0—3.333333333333333333……由于cout打印6位小数，因此 tub和mint都是精确的。但当程序将每个数乘以一百万后，tub在第7个3 之后就与正确的值有了误差。tub在7位有效位上还是精确的（该系统确 保float至少有6位有效位，但这是最糟糕的情况）。然而，double类型的 变量显示了13个3，因此它至少有13位是精确的。由于系统确保15位有效位，因此这就没有什么好奇怪的了。另外，将tub乘以一百万，再乘以10后，得到的结果不正确，这再一次指出了float的精度限制。

 

#### 浮点常量

在程序中书写浮点常量的时候，程序将把它存储为哪种浮点类型呢？在默认情况下，像8.24和2.4E8这样的浮点常量都属于double类型。 如果希望常量为float类型，请使用f或F后缀。对于long double类型，可使用l或L后缀（由于l看起来像数字1，因此L是更好的选择）。下面是 一些示例： 

```
1.234f //a float constant;
2.45E20F //a float constant;
2.345324E28 //a double constant;
2.2L //a long double constant;
```

#### 浮点数的优缺点

与整数相比，浮点数有两大优点。首先，它们可以表示整数之间的 值。其次，由于有缩放因子，它们可以表示的范围大得多。另一方面， 浮点运算的速度通常比整数运算慢，且精度将降低

```cpp
#include <iostream>
int main()
{
  using namespace std;
  float a = 2.34E+22f;
  float b = a + 1.0f;
  cout << "a = " << a << endl;
  cout << "b - a = " << b - a << endl;
  return 0;
}
```

**有些基于ANSI C之前的编译器的老式C++实现不支持浮点常量后缀f。如果出现这样的问题， 可以用2.34E+22代替2.34E+22f，用(float) 1.0代替1.0f。**

该程序将数字加1，然后减去原来的数字。结果应该为1。下面是在 某个系统上运行时该程序的输出：

```
a=2.34e+022
b-a=0
```

问题在于，2.34E+22是一个小数点左边有23位的数字。加上1，就 是在第23位加1。但float类型只能表示数字中的前6位或前7位，因此修 改第23位对这个值不会有任何影响。

C++对基本类型进行分类，形成了若干个族。类型signed char、short、int和long统称为符 号整型；它们的无符号版本统称为无符号整型；C++11新增了long long。bool、char、 wchar_t、符号整数和无符号整型统称为整型；C++11新增了char16_t和char32_t。float、double和long double统称为浮点型。整数和浮点型统称算术（arithmetic）类型

## C++算术运算符 

C++使用运算符来运算。它提供了几种运算符来完成5 种基本的算术计算：加法、减法、乘法、除法以及求模。每种运算符都使用两个值（操作数）来计算结果。运算符及其操作数构成了表达式。

下面是5种基本的C++算术运算符。

1. +运算符对操作数执行加法运算。例如，4+20等于24。 
2. −运算符从第一个数中减去第二个数。例如，12−3等于9。 
3. 运算符将操作数相乘。例如，*28*4等于112。 
4. /运算符用第一个数除以第二个数。例如，1000/5等于200。如果两 个操作数都是整数，则结果为商的整数部分。例如，17/3等于5， 小数部分被丢弃。 
5. %运算符求模。也就是说，它生成第一个数除以第二个数后的余 数。例如，19%6为1，因为19是6的3倍余1。两个操作数必须都是 整型，将该运算符用于浮点数将导致编译错误。如果其中一个是负 数，则结果的符号满足如下规则：(a/b)*b + a%b ＝ a。 

```cpp
#include <iostream>
int main()
{
  using namespace std;
  float hats, heads;
  cout.setf(ios::fixed, ios_base::floatfield);
  cout << "enter a number : ";
  cin >> hats;
  cout << "enter another number : ";
  cin >> heads;

  cout << "hats = " << hats << "; heads = " << heads << endl;
  cout << "hats + heads = " << hats + heads << endl;
  cout << "hats - heads = " << hats - heads << endl;
  cout << "hats / heads = " << hats / heads << endl;
  cout << "hats * heads = " << hats * heads << endl;

  return 0;
}

// enter a number : 50.25
// enter another number : 11.17
// hats = 50.250000; heads = 11.170000
// hats + heads = 61.419998
// hats - heads = 39.080002
// hats / heads = 4.498657
// hats * heads = 561.292480
```

11.17加上50.25应等于61.42，但 是输出中却是61.419998。这不是运算问题；而是由于float类型表示有效位数的能力有限。记住，对于float，C++只保证6位有效位。如果将 61.419998四舍五入成6位，将得到61.4200，这是保证精度下的正确值。如果需要更高的精度，请使用double或long double。

```js
#include <iostream>
int main()
{
  using namespace std;
  cout.setf(ios_base::fixed, ios_base::floatfield);
  cout << "interger division:9/5 = " << 9 / 5 << endl;
  cout << "floating-point : 9.0/5.0 = " << 9.0 / 5.0 << endl;
  cout << "mixed division: 9.0/5 = " << 9.0 / 5 << endl;
  cout << "double constants : 1e7/9.0 = " << 1.e7 / 9.0 << endl;
  cout << "float constants: 1e7f/9.0f = ";
  cout << 1.e7f / 9.0f << endl;
  return 0;
}

// interger division:9/5 = 1
// floating-point : 9.0/5.0 = 1.800000
// mixed division: 9.0/5 = 1.800000
// double constants : 1e7/9.0 = 1111111.111111
// float constants: 1e7f/9.0f = 1111111.125000
```

整数9除以5的结果为整数1。4/5的小数部分 （或0.8）被丢弃。在本章后面学习求模运算符时，将会看到这种除法的实际应用。接下来的两行表明，当至少有一个操作数是浮点数时，结 果为1.8。实际上，对不同类型进行运算时，C++将把它们全部转换为同 一类型。本章稍后将介绍这种自动转换。最后两行的相对精度表明，如 果两个操作数都是double类型，则结果为double类型；如果两个操作数 都是float类型，则结果为float类型。记住，浮点常量在默认情况下为 double类型。 

除法运算符表示了3种不同的运算：int除法、float除法和double除法。 

C++根据上下文（这里是操作数的类型）来确定运算符的含义。使用相同的符号进行多种操作 叫做运算符重载（operator overloading）。

```cpp
#include <iostream>
int main()
{
  using namespace std;
  const int Lbs_per_stn = 14;
  int lbs;
  cout << "enter your weight in pounds: ";
  cin >> lbs;
  int stone = lbs / Lbs_per_stn;
  int pounds = lbs % Lbs_per_stn;
  cout << lbs << " pound are " << stone << " stone " << pounds << "pound(s). \n";
  return 0;
}

// enter your weight in pounds: 181
// 181 pound are 12 stone 13pound(s).
```

#### 类型转换 

C++自动执行很多类型转换：

将一种算术类型的值赋给另一种算术类型的变量时，C++将对值进 行转换； 

表达式中包含不同的类型时，C++将对值进行转换； 

将参数传递给函数时，C++将对值进行转换

C++允许将一种类型的值赋给另一种类型的变量。这样做时，值将 被转换为接收变量的类型。例如，假设so_long的类型为long，thirty的类 型为short，而程序中包含这样的语句：

```
so_long=thirty;
```

则进行赋值时，程序将thirty的值（通常是16位）扩展为long值（通 常为32位）。扩展后将得到一个新值，这个值被存储在so_long中，而 thirty的内容不变。

将一个值赋给值取值范围更大的类型通常不会导致什么问题。例 如，将short值赋给long变量并不会改变这个值，只是占用的字节更多而 已。然而，将一个很大的long值（如2111222333）赋给float变量将降低 精度。因为float只有6位有效数字，因此这个值将被四舍五入为 2.11122E9。因此，有些转换是安全的，有些则会带来麻烦。表3.3列出 了一些可能出现的转换问题

| 转换                                                         | 潜在问题                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 将较大的浮点类型转换为较小的 浮点类型，如将double转换为float | 精度（有效数位）降低，值可能超出目标类型的取值范围，在这种情况下，结果将是不确定的 |
| 将浮点类型转换为整型                                         | 小数部分丢失，原来的值可能超出目标类型的取值范围，在这种情况下，结果将是不确定的 |
| 将较大的整型转换为较小的整 型，如将long转换为short           | 原来的值可能超出目标类型的取值范围，通常只复制右边的字节     |

将0赋给bool变量时，将被转换为false；而非零值将被转换为true。将浮点值赋给整型将导致两个问题。首先，将浮点值转换为整型会 

将数字截短（除掉小数部分）。其次，float值对于int变量来说可能太大 了。在这种情况下，C++并没有定义结果应该是什么；这意味着不同的 实现的反应可能不同。

```c++
#include <iostream>
int main()
{
  using namespace std;
  cout.setf(ios_base::fixed, ios_base::floatfield);
  float tree = 3;
  int guess(3.9832);
  int debt = 7.2E12;
  cout << " tree = " << tree << endl;
  cout << " guess = " << guess << endl;
  cout << " debt= " << debt << endl;
  return 0;
}
/**
tree = 3.000000
 guess = 3
 debt= 1
*/
```

在这个程序中，将浮点值3.0赋给了tree。将3.9832赋给int变量guess 导致这个值被截取为3。将浮点型转换为整型时，C++采取截取（丢弃 小数部分）而不是四舍五入（查找最接近的整数）。最后，int变量debt 无法存储3.0E12，这导致C++没有对结果进行定义的情况发生。在这种 系统中，debt的结果为1634811904，或大约1.6E09。 当您将整数变量初始化为浮点值时，有些编译器将提出警告，指出 这可能丢掉数据。另外，对于debt变量，不同编译器显示的值也可能不 同。例如，在另一个系统上运行该程序时，得到的值为2147483647。

##### 以**{ }**方式初始化时进行的转换

C++11将使用大括号的初始化称为列表初始化（list- initialization），因为这种初始化常用于给复杂的数据类型提供值列表

具体地说，列表初始化不允许缩窄（narrowing），即变量的类型可能无法表示赋给它的值。例如，不允许将浮点型转换为整型。在不同的整型 之间转换或将整型转换为浮点型可能被允许，条件是编译器知道目标变量能够正确地存储赋给它的值。例如，可将long变量初始化为int值，因为long总是至少与int一样长；相反方向的转换也可能被允许，只要int变量能够存储赋给它的long常量： 

```cpp
#include <iostream>
int main()
{
  using namespace std;
  const int code = 66;
  int x = 66;
  char c1 = {66};
  char c2 = {313155};
  char c3 = {code};
  char c4 = {x};
  x = 31325;
  char c5 = x;
  return 0;
}
```

在上述代码中，初始化c4时，您知道x的值为66，但在编译器看来，x是一个变量，其值可能很大。编译器不会跟踪下述阶段可能发生 的情况：从x被初始化到它被用来初始化c4。

##### 表达式中的转换 

当同一个表达式中包含两种不同的算术类型时，将出现什么情况 呢？在这种情况下，C++将执行两种自动转换：首先，一些类型在出现 时便会自动转换；其次，有些类型在与其他类型同时出现在表达式中时 将被转换。 

先来看看自动转换。在计算表达式时，C++将bool、char、unsigned char、signed char和short值转换为int。具体地说，true被转换为1，false 被转换为0。这些转换被称为整型提升（integral promotion）。例如，请 看下面的语句： 

```cpp
	short chickens = 20;
  short ducks = 35;
  short fowl = chickens + ducks;
```

为执行第3行语句，C++程序取得chickens和ducks的值，并将它们转 换为int。然后，程序将结果转换为short类型，因为结果将被赋给一个 short变量。这种说法可能有点拗口，但是情况确实如此。通常将int类型 选择为计算机最自然的类型，这意味着计算机使用这种类型时，运算速度可能最快。 

还有其他一些整型提升：如果short比int短，则unsigned short类型将 被转换为int；如果两种类型的长度相同，则unsigned short类型将被转换 为unsigned int。这种规则确保了在对unsigned short进行提升时不会损失数据。

同样，wchar_t被提升成为下列类型中第一个宽度足够存储wchar_t 取值范围的类型：int、unsigned int、long或unsigned long。将不同类型进行算术运算时，也会进行一些转换，例如将int和float 相加时。当运算涉及两种类型时，较小的类型将被转换为较大的类型

（1）如果有一个操作数的类型是long double，则将另一个操作数 转换为long double。 

（2）否则，如果有一个操作数的类型是double，则将另一个操作 数转换为double。 

（3）否则，如果有一个操作数的类型是float，则将另一个操作数 转换为float。 

（4）否则，说明操作数都是整型，因此执行整型提升

（5）在这种情况下，如果两个操作数都是有符号或无符号的，且 其中一个操作数的级别比另一个低，则转换为级别高的类型。

（6）如果一个操作数为有符号的，另一个操作数为无符号的，且 无符号操作数的级别比有符号操作数高，则将有符号操作数转换为无符 

号操作数所属的类型。

（7）否则，如果有符号类型可表示无符号类型的所有可能取值， 则将无符号操作数转换为有符号操作数所属的类型。 

（8）否则，将两个操作数都转换为有符号类型的无符号版本。 

ANSI C遵循的规则与ISO 2003 C++相同，这与前述规则稍有不同； 而传统K&R C的规则又与ANSI C稍有不同。例如，传统C语言总是将 float提升为double，即使两个操作数都是float。

前面的列表谈到了整型级别的概念。简单地说，有符号整型按级别 从高到低依次为long long、long、int、short和signed char。无符号整型 的排列顺序与有符号整型相同。类型char、signed char和unsigned char的 级别相同。类型bool的级别最低。wchar_t、char16_t和char32_t的级别与其底层类型相同。

##### 传递参数时的转换

传递参数时的类型转换通常由C++函数原型,控制。然而，也可以取消原型对参数传递的控制，尽管这样做并不明 智。在这种情况下C++将对char和short类型（signed和unsigned）应用 整型提升。另外，为保持与传统C语言中大量代码的兼容性，在将参数 传递给取消原型对参数传递控制的函数时，C++将float参数提升为 double

##### 强制类型转换

C++还允许通过强制类型转换机制显式地进行类型转换。（C++认识到，必须有类型规则，而有时又需要推翻这些规则。）强制类型转换 的格式有两种。例如，为将存储在变量thorn中的int值转换为long类型,可以使用下述表达式中的一种： 

```cpp
(long) thron; //returns a type long conversion of thorn
long (thron) //returns a type long conversion of thorn
```

强制类型转换不会修改thorn变量本身，而是创建一个新的、指定类 型的值，可以在表达式中使用这个值。

第一种格式来自C语言，第二种格式是纯粹的C++。新格式的想法 是，要让强制类型转换就像是函数调用。这样对内置类型的强制类型转 换就像是为用户定义的类设计的类型转换

C++还引入了4个强制类型转换运算符，对它们的使用要求更为严 格，这将在第15章介绍。在这四个运算符中，static_cast<>可用于将值 从一种数值类型转换为另一种数值类型。例如，可以像下面这样将thorn 转换为long类型： 

```cpp
    static_cast<long> (thron);//returns a type long conversion of thron
```

推而广之，可以这样做：

```
static_cast<typeName> (value);
```

```cpp
#include <iostream>

int main() {
    using namespace std;
    int auks ,bats,coots;
    auks=19.99+11.99;
    bats=(int)19.99+(int)11.99;
    coots=int (19.99) + int (11.99);
    cout << "auks = "<<auks<<" , bats = "<<bats;
    cout << " , coots = " <<coots<<endl;

    char ch='Z';
    cout << " The code for " << ch << "is "<< int(ch) <<endl;
    cout << " yes the code is "<<static_cast<int>(ch);
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

该程序指出了使用强制类型转换的两个原因。首先，可能有一些值 被存储为double类型，但要使用它们来计算得到一个int类型的值。例 如，可能要用浮点数来对齐网格或者模拟整数值（如人口）。程序员可 能希望在计算时将值视为int，强制类型转换允许直接这样做。注意，将 值转换为int，然后相加得到的结果，与先将值相加，然后转换为int是不 同的，至少对于这些值来说是不同的。

#### C++11中的auto声明

C++11新增了一个工具，让编译器能够根据初始值的类型推断变量 的类型。为此，它重新定义了auto的含义。auto是一个C语言关键字，但 很少使用。在初始化声明中，如果 使用关键字auto，而不指定变量的类型，编译器将把变量的类型设置成 与初始值相同：

```
 auto n=100; //int
 auto x=1.5; //double
 auto y=1.3e12L; //long double
```

然而，自动推断类型并非为这种简单情况而设计的；事实上，如果 将其用于这种简单情形，甚至可能让您误入歧途。例如，假设您要将 x、y和z都指定为double类型，并编写了如下代码：

```
auto x = 0.0; //x is double because 0.0
double y = 0; //0 automatically converted to 0.0;
auto z = 0; //z is int because 0 is int;
```

显式地声明类型时，将变量初始化0（而不是0.0）不会导致任何问题，但采用自动类型推断时，这却会导致问题。

## 总结

C++的基本类型分为两组：一组由存储为整数的值组成，另一组由 存储为浮点格式的值组成。整型之间通过存储值时使用的内存量及有无 符号来区分。整型从最小到最大依次是：bool、char、signed char、 unsigned char、short、unsigned short、int、unsigned int、long、unsigned long以及C++11新增的long long和unsigned long long。还有一种wchar_t 类型，它在这个序列中的位置取决于实现。C++11新增了类型char16_t 和char32_t，它们的宽度足以分别存储16和32位的字符编码。C++确保 了char足够大，能够存储系统基本字符集中的任何成员，而wchar_t则可 以存储系统扩展字符集中的任意成员，short至少为16位，而int至少与 short一样长，long至少为32位，且至少和int一样长。确切的长度取决于 实现。

字符通过其数值编码来表示。I/O系统决定了编码是被解释为字符 还是数字。浮点类型可以表示小数值以及比整型能够表示的值大得多的值。3 种浮点类型分别是float、double和long double。C++确保float不比double 长，而double不比long double长。通常，float使用32位内存，double使用 64位，long double使用80到128位

通过提供各种长度不同、有符号或无符号的类型，C++使程序员能 够根据特定的数据要求选择合适的类型

C++使用运算符来提供对数字类型的算术运算：加、减、乘、除和 求模。当两个运算符对同一个操作数进行操作时，C++的优先级和结合 性规则可以确定先执行哪种操作。

对变量赋值、在运算中使用不同类型、使用强制类型转换时， C++将把值从一种类型转换为另一种类型。很多类型转换都是“安全 的”，即可以在不损失和改变数据的情况下完成转换。例如，可以把int 值转换为long值，而不会出现任何问题。对于其他一些转换，如将浮点 类型转换为整型，则需要更加小心。
