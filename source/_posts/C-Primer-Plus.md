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

# 复合类型 

## 数组

要创建数组，可使用声明语句。数组声明应指出以下三点：

存储在每个元素中的值的类型； 

数组名； 

数组中的元素数

在C++中，可以通过修改简单变量的声明，添加中括号（其中包含元素数目）来完成数组声明。例如，下面的声明创建一个名为months的 数组，该数组有12个元素，每个元素都可以存储一个short类型的值

```cpp
short months[12]
```

事实上，可以将数组中的每个元素看作是一个简单变量。 声明数组的通用格式如下

```
typeName srrayName[arraySize];
```

表达式arraySize指定元素数目，它必须是整型常数（如10）或const 值，也可以是常量表达式（如8 * sizeof（int）），即其中所有的值在编 译时都是已知的。具体地说，arraySize不能是变量，变量的值是在程序 运行时设置的。然而，可以使用new运算符来避开这种限制。

数组之所以被称为复合类型，是因为它是使用其他类型来创建的（C语言使用术语“派生 类型”，但由于C++对类关系使用术语“派生”，所以它必须创建一个新术语）。不能仅仅将某 种东西声明为数组，它必须是特定类型的数组。没有通用的数组类型，但存在很多特定的数组类型，如char数组或long数组。例如，请看下面的声明： 

```cpp
float loans[20]
```

loans的类型不是“数组”，而是“float数组”。这强调了loans数组是使用float类型创建的

数组的很多用途都是基于这样一个事实：可以单独访问数组元素。 方法是使用下标或索引来对元素进行编号。C++数组从0开始编号（这 没有商量的余地，必须从0开始。Pascal和BASIC用户必须调整习惯）。 C++使用带索引的方括号表示法来指定数组元素。例如，months[0]是 months数组的第一个元素，months[11]是最后一个元素。注意，最后一 个元素的索引比数组长度小1（参见图4.1）。因此，数组声明能够使用 一个声明创建大量的变量，然后便可以用索引来标识和访问各个元素。

编译器不会检查使用的下标是否有效。例如，如果将一个值赋给不存在的元素 months[101]，编译器并不会指出错误。但是程序运行后，这种赋值可能引发问题，它可能破坏数据或代码，也可能导致程序异常终止。所以必须确保程序只使用有效的下标值

```cpp
#include <iostream>
int main(){
    using namespace std;
    int yams[3];
    yams[0]=7;
    yams[1]=8;
    yams[2]=6;
    int arr[3]={20,30,5};
    cout << "total yams = ";
    cout << yams[0] + yams[1]+yams[2]<<endl;
    cout << "the package with " << yams[1] << " yams costs";
    cout << arr[1] << " cents per yam. \n";
    int total=yams[0]*arr[0]+yams[1]*arr[1];
    total=total+yams[2]*arr[2];
    cout << "the total yam expense is " << total << " cents . \n";
    cout << "\n Size of yams array =" <<sizeof yams;
    cout << " bytes. \n";
    cout << "Size of one element " << sizeof yams[0];
    cout << " bytes . \n";
    return 0;
}

//total yams = 21
//the package with 8 yams costs30 cents per yam.
//the total yam expense is 410 cents .
//
//Size of yams array =12 bytes.
//Size of one element 4 bytes . 
```

sizeof运算符返回类型或数据对象的长度（单位为字节）。注意，如果将sizeof运算符用于数组名，得到的将是整个数组中的字节数。但如果将sizeof用于数组元素，则得到的将是元素的长度（单位为字节）

### 数组的初始化规则 

只有在定义数组时才能使用初始化，此后就不能使用了，也不能将 一个数组赋给另一个数组： 

```cpp
int cards[4]={3,4,5,6};//okey
int hand[4]; //okey
hand[4]={5,6,7,9}; //not allowed
hand=cards; //not allowed
```

然而，可以使用下标分别给数组中的元素赋值。初始化数组时，提供的值可以少于数组的元素数目

```cpp
float hoteltips[5]={5.0,2.5};
```

如果只对数组的一部分进行初始化，则编译器将把其他元素设置为 0。因此，将数组中所有的元素都初始化为0非常简单—只要显式地将第 一个元素初始化为0，然后让编译器将其他元素都初始化为0即可

```cpp
long totals[500]={0};
```

如果初始化为{1}而不是{0}，则第一个元素被设置为1，其他元素 都被设置为0。

如果初始化数组时方括号内（[ ]）为空，C++编译器将计算元素个 数。例如，对于下面的声明：

```cpp
short things[]={1,2,3,4};
```

通常，让编译器计算元素个数是种很糟的做法，因为其计数可能与您想象的不一样。例 如，您可能不小心在列表中遗漏了一个值。然而，这种方法对于将字符数组初始化为一个字 符串来说比较安全，很快您将明白这一点。如果主要关心的问题是程序，而不是自己是否知 道数组的大小，则可以这样做： 

```cpp
 short things[]={1,5,4,6};
 int num_elements=sizeof things/ sizeof(short);
```

### C++11数组初始化方法

C++11将使用大括号的初始化（列表初始化）作为一 种通用初始化方式，可用于所有类型。数组以前就可使用列表初始化， 但C++11中的列表初始化新增了一些功能。 

首先，初始化数组时，可省略等号（=）： 

```cpp
double earnings[4] {1.2e4,1.3e4,1.3e6,1.6e4};
```

可不在大括号内包含任何东西，这将把所有元素都设置为零

```cpp
usinged int couts[10]={};
float balance[100] {};
```

列表初始化禁止缩窄准换

```cpp
long printf[]={90,25,3.4}; //not allowed
char shifts[4] {'h','i',1122011,'\0'}; //not allowed
char tlifs[4] {'h','i',112,'\0'}; //allowed
```

在上述代码中，第一条语句不能通过编译，因为将浮点数转换为整 型是缩窄操作，即使浮点数的小数点后面为零。第二条语句也不能通过 编译，因为1122011超出了char变量的取值范围（这里假设char变量的长 度为8位）。第三条语句可通过编译，因为虽然112是一个int值，但它在 char变量的取值范围内。

C++标准模板库（STL）提供了一种数组替代品—模板类vector，而C++11新增了模板类array。这些替代品比内置复合类型数组更复杂、更灵活

## 字符串

字符串是存储在内存的连续字节中的一系列字符。C++处理字符串 的方式有两种。第一种来自C语言，常被称为C-风格字符串（C-style string），然后介绍另一种基于string类库的方法

存储在连续字节中的一系列字符意味着可以将字符串存储在char数 组中，其中每个字符都位于自己的数组元素中。字符串提供了一种存储 文本信息的便捷方式，如提供给用户的消息（“请告诉我您的瑞士银行 账号”）或来自用户的响应（“您肯定在开玩笑”）。C-风格字符串具有 一种特殊的性质：以空字符（null character）结尾，空字符被写作\0， 其ASCII码为0，用来标记字符串的结尾。例如，请看下面两个声明：

```cpp
 char dog[8]={'s','t','r','i','n','g','s','o'};//not a string
 char cat[8]={'s','t','r','i','n','g','a','\0'};//is string
```

这两个数组都是char数组，但只有第二个数组是字符串。空字符对 C-风格字符串而言至关重要。例如，C++有很多处理字符串的函数，其 中包括cout使用的那些函数。它们都逐个地处理字符串中的字符，直到到达空字符为止。如果使用cout显示上面的cat这样的字符串，则将显示前7个字符，发现空字符后停止。但是，如果使用cout显示上面的dog数组（它不是字符串），cout将打印出数组中的8个字母，并接着将内存中随后的各个字节解释为要打印的字符，直到遇到空字符为止。由于空 字符（实际上是被设置为0的字节）在内存中很常见，因此这一过程将 很快停止。但尽管如此，还是不应将不是字符串的字符数组当作字符串来处理。

将数组初始化成单个字符数字很不好写，一般不使用这种写法，一般使用字符串常量（string constant）或字符串字面值 （string literal）

```cpp
 char birds[11]="Mr. cheeps"; //必须使用双引号，不能使用单引号
 char fish[]="fish";
```

用引号括起的字符串隐式地包括结尾的空字符，因此不用显式地包 括它。另外，各种C++输入工具通过键盘输入，将字符串 读入到char数组中时，将自动加上结尾的空字符

当然，应确保数组足够大，能够存储字符串中所有字符—包括空字 符。使用字符串常量初始化字符数组是这样的一种情况，即让编译器计算元素数目更为安全。让数组比字符串长没有什么害处，只是会浪费一些空间而已。这是因为处理字符串的函数根据空字符的位置，而不是数组长度来进行处理。C++对字符串长度没有限制。 

在确定存储字符串所需的最短数组时，别忘了将结尾的空字符计算在内。

数组初始化字符串时，剩余的空间会用\0填补注意，字符串常量（使用双引号）不能与字符常量（使用单引号） 互换。字符常量（如'S'）是字符串编码的简写表示。在ASCII系统上，'S'只是83的另一种写法，因此，下面的语句将83赋给shirt_size：

```
char shirt_size = 'S'
```

但"S"不是字符常量，它表示的是两个字符（字符S和\0）组成的字符串,"S"实际上表示的是字符串所在的内存地址

```
char shirt_size = "S" //这样是不允许的
```

由于地址在C++中是一种独立的类型，因此C++编译器不允许这种不合理的做法

### 拼接字符串常量

C++允许拼接字符串字面值，即将两个用引号括起的字符串合并为一个。事实上，任何两个由空白（空格、制表符和换行符）分隔的字符串常量都将自动拼接成一个。拼接时不会在被连接的字符串之间添加空格，第二个字符串 的第一个字符将紧跟在第一个字符串的最后一个字符（不考虑\0）后 面。第一个字符串中的\0字符将被第二个字符串的第一个字符取代。

### 在数组中使用字符串

要将字符串存储到数组中，最常用的方法有两种—将数组初始化为 字符串常量、将键盘或文件输入读入到数组中。程序清单4.2演示了这 两种方法，它将一个数组初始化为用引号括起的字符串，并使用cin将 一个输入字符串放到另一个数组中。该程序还使用了标准C语言库函数 strlen( )来确定字符串的长度。标准头文件cstring（老式实现为string.h） 提供了该函数以及很多与字符串相关的其他函数的声明。 

```cpp
// strings.cpp -- storing strings in an array
#include <iostream>
#include <cstring>  // for the strlen() function
int main()
{
    using namespace std;
    const int Size = 15;
    char name1[Size];               // empty array
    char name2[Size] = "C++owboy";  // initialized array
    // NOTE: some implementations may require the static keyword
    // to initialize the array name2

    cout << "Howdy! I'm " << name2;
    cout << "! What's your name?\n";
    cin >> name1;
    cout << "Well, " << name1 << ", your name has ";
    cout << strlen(name1) << " letters and is stored\n";
    cout << "in an array of " << sizeof(name1) << " bytes.\n";
    cout << "Your initial is " << name1[0] << ".\n";
    name2[3] = '\0';                // set to null character
    cout << "Here are the first 3 characters of my name: ";
    cout << name2 << endl;
    // cin.get();
    // cin.get();
    return 0;
}
//Howdy! I'm C++owboy! What's your name?
//basicman
//        Well, basicman, your name has 8 letters and is stored
//        in an array of 15 bytes.
//Your initial is b.
//Here are the first 3 characters of my name: C++
```

首先，sizeof运算符指出整个数组的长度：15字节，但 strlen( )函数返回的是存储在数组中的字符串的长度，而不是数组本身的长度。另外，strlen( ) 只计算可见的字符，而不把空字符计算在内。因此，对于Basicman，返回的值为8，而不是 9。如果cosmic是字符串，则要存储该字符串，数组的长度不能短于strlen（cosmic）+1。

由于name1和name2是数组，所以可以用索引来访问数组中各个字符。例如，该程序使用 name1[0]找到数组的第一个字符。另外，该程序将name2[3]设置为空字符。这使得字符串在第 3个字符后即结束，虽然数组中还有其他的字符

该程序使用符号常量来指定数组的长度。程序常常有多条语句使用 了数组长度。使用符号常量来表示数组长度后，当需要修改程序以使用 不同的数组长度时，工作将变得更简单—只需在定义符号常量的地方进 行修改即可。

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-11%2022.40.37.png)

### 字符串输入

```cpp
#include <iostream>
int main()
{
    using namespace std;
    const int ArSize = 20;
    char name[ArSize];
    char dessert[ArSize];

    cout << "Enter your name:\n";
    cin >> name;
    cout << "Enter your favorite dessert:\n";
    cin >> dessert;
    cout << "I have some delicious " << dessert;
    cout << " for you, " << name << ".\n";
    // cin.get();
    // cin.get();
    return 0;
}
//Enter your name:
//Alistair breeb
//Enter your favorite dessert:
//I have some delicious breed for you, Alistair.
```

cin是如何确定已完成字符串输入呢？由于不能通过键盘输入空字 符，因此cin需要用别的方法来确定字符串的结尾位置。cin使用空白 （空格、制表符和换行符）来确定字符串的结束位置，这意味着cin在 获取字符数组输入时只读取一个单词。读取该单词后，cin将该字符串放到数组中，并自动在结尾添加空字符

这个例子的实际结果是，cin把Alistair作为第一个字符串，并将它 放到name数组中。这把Dreeb留在输入队列中。当cin在输入队列中搜索 用户喜欢的甜点时，它发现了Dreeb，因此cin读取Dreeb，并将它放到 dessert数组中

### 每次读取一行字符串输入

#### 面向行的输入：**getline( )** 

getline( )函数读取整行，它使用通过回车键输入的换行符来确定输 入结尾。要调用这种方法，可以使用cin.getline( )。该函数有两个参数。第一个参数是用来存储输入行的数组的名称，第二个参数是要读取的字 符数。如果这个参数为20，则函数最多读取19个字符，余下的空间用于存储自动在结尾处添加的空字符。getline( )成员函数在读取指定数目的字符或遇到换行符时停止读取。

例如，假设要使用getline( )将姓名读入到一个包含20个元素的name 数组中。可以使用这样的函数调用： 

```
cin.getline(name,20);//实际只能存19个元素
```

```cpp
#include <iostream>
int main()
{
    using namespace std;
    const int ArSize = 20;
    char name[ArSize];
    char dessert[ArSize];

    cout << "Enter your name:\n";
    cin.getline(name, ArSize);  // reads through newline
    cout << "Enter your favorite dessert:\n";
    cin.getline(dessert, ArSize);
    cout << "I have some delicious " << dessert;
    cout << " for you, " << name << ".\n";
    // cin.get();
    return 0;
}
//Enter your name:
//marry willion
//Enter your favorite dessert:
//katy bell
//I have some delicious katy bell for you, marry willion.
```

getline( )函 数每次读取一行。它通过换行符来确定行尾，但不保存换行符。相反,在存储字符串时，它用空字符来替换换行符

#### 面向行的输入：get( )

istream类有另一个名为get( )的成员函数,该函数有几种变体。其中一种变体的工作方式与getline( )类似，它们接受的参数相同，解释参数的方式也相同，并且都读取到行尾。但get并不再读取并丢弃换行符，而是将其留在输入队列中。假设我们连续两次调用get( )：

```
cin.get(name,ArSize);
cin.get(dessert,ArSize);
```

由于第一次调用后，换行符将留在输入队列中，因此第二次调用时 看到的第一个字符便是换行符。因此get( )认为已到达行尾，而没有发现任何可读取的内容。如果不借助于帮助，get( )将不能跨过该换行符。 

get( )有另一种变体。使用不带任何参数的cin.get( )调用 可读取下一个字符（即使是换行符），因此可以用它来处理换行符，为 读取下一行输入做好准备。也就是说，可以采用下面的调用序列：

```
cin.get(name,ArSize);
cin.get();
cin.get(dessert,ArSize);
```

另一种使用get( )的方式是将两个类成员函数拼接起来（合并），如下所示：

```
cin.get(name,ArSize).get();//get return cin 可以进行链式调用
```

之所以可以这样做，是由于cin.get（name，ArSize）返回一个cin对象，该对象随后将被用来调用get( )函数。同样，下面的语句将把输入中连续的两行分别读入到数组name1和name2 中，其效果与两次调用 cin.getline( )相同：

```
cin.getline(name1,name2).getline(name1,name2);
```

```cpp
// instr3.cpp -- reading more than one word with get() & get()
#include <iostream>
int main()
{
    using namespace std;
    const int ArSize = 20;
    char name[ArSize];
    char dessert[ArSize];

    cout << "Enter your name:\n";
    cin.get(name, ArSize).get();    // read string, newline
    cout << "Enter your favorite dessert:\n";
    cin.get(dessert, ArSize).get();
    cout << "I have some delicious " << dessert;
    cout << " for you, " << name << ".\n";
    // cin.get();
    return 0; 
}
```

需要指出的一点是，C++允许函数有多个版本，条件是这些版本的 参数列表不同。如果使用的是cin.get（name，ArSize），则编译器知道 是要将一个字符串放入数组中，因而将使用适当的成员函数。如果使用 的是cin.get( )---函数重载

为什么要使用get( )，而不是getline( )呢？首先，老式实现没有 getline( )。其次，get( )使输入更仔细。例如，假设用get( )将一行读入数 组中。如何知道停止读取的原因是由于已经读取了整行，而不是由于数组已填满呢？查看下一个输入字符，如果是换行符，说明已读取了整行；否则，说明该行中还有其他输入

总之， getline( )使用起来简单一些，但get( )使得检查错误更简单些。可以用其中的任何一个来读取一行输入；只是应该知道，它们的行为稍有不同。

#### 空行和其他问题

当getline( )或get( )读取空行时，将发生什么情况？最初的做法是， 下一条输入语句将在前一条getline( )或get( )结束读取的位置开始读取； 但当前的做法是，当get( )（不是getline( )）读取空行后将设置失效位 （failbit）。这意味着接下来的输入将被阻断，但可以用下面的命令来 恢复输入：

```
cin.clear()
```

另一个潜在的问题是，输入字符串可能比分配的空间长。如果输入 行包含的字符数比指定的多，则getline( )和get( )将把余下的字符留在输 入队列中，而getline( )还会设置失效位，并关闭后面的输入

### 混合输入字符串和数字

混合输入数字和面向行的字符串会导致问题

```cpp
#include <iostream>
int main()
{
    using namespace std;
    cout << "What year was your house built?\n";
    int year;
    cin >> year;
    // cin.get();
    cout << "What is its street address?\n";
    char address[80];
    cin.getline(address, 80);
    cout << "Year built: " << year << endl;
    cout << "Address: " << address << endl;
    cout << "Done!\n";
    // cin.get();
    return 0;
}
//What year was your house built?
//1966
//What is its street address?
//Year built: 1966
//Address:
//Done!
```

用户根本没有输入地址的机会。问题在于，当cin读取年份，将回 车键生成的换行符留在了输入队列中。后面的cin.getline( )看到换行符 后，将认为是一个空行，并将一个空字符串赋给address数组。解决办法是在读取地址之前先读取并丢弃换行符.使用get()或者get(ch)

```
cin>>year;
cin.get()//cin.get(ch)

///////////////////
(cin>>year).get()//get(ch)
```

C++程序常使用指针（而不是数组）来处理字符串

```cpp
#include <iostream>
int main()
{
    using namespace std;
    cout << "What year was your house built?\n";
    int year;
    cin >> year;
    cin.get();
    cout << "What is its street address?\n";
    char address[80];
    cin.getline(address, 80);
    cout << "Year built: " << year << endl;
    cout << "Address: " << address << endl;
    cout << "Done!\n";
    cin.get();
    return 0;
}
//What year was your house built?
//1966
//What is its street address?
//built 122
//Year built: 1966
//Address: built 122
//Done!
```

## string类简介 

ISO/ANSI C++98标准通过添加string类扩展了C++库，因此现在可 以string类型的变量（使用C++的话说是对象）而不是字符数组来存储字符串。您将看到，string类使用起来比数组简单，同时提供了将字符串作为一种数据类型的表示方法。 

要使用string类，必须在程序中包含头文件string。string类位于名称 空间std中，因此您必须提供一条using编译指令，或者使用std::string来引用它。string类定义隐藏了字符串的数组性质，让您能够像处理普通变量那样处理字符串

```cpp
// strtype1.cpp -- using the C++ string class
#include <iostream>
#include <string>               // make string class available
int main()
{
    using namespace std;
    char charr1[20];            // create an empty array
    char charr2[20] = "jaguar"; // create an initialized array
    string str1;                // create an empty string object
    string str2 = "panther";    // create an initialized string

    cout << "Enter a kind of feline: ";
    cin >> charr1;
    cout << "Enter another kind of feline: ";
    cin >> str1;                // use cin for input
    cout << "Here are some felines:\n";
    cout << charr1 << " " << charr2 << " "
         << str1 << " " << str2 // use cout for output
         << endl;
    cout << "The third letter in " << charr2 << " is "
         << charr2[2] << endl;
    cout << "The third letter in " << str2 << " is "
         << str2[2] << endl;    // use array notation
    // cin.get();
    // cin.get();

    return 0;
}
//Enter a kind of feline: ocelot
//Enter another kind of feline: tiger
//Here are some felines:
//ocelot jaguar tiger panther
//The third letter in jaguar is g
//The third letter in panther is n
```

从这个示例可知，在很多方面，使用string对象的方式与使用字符数组相同。

可以使用C-风格字符串来初始化string对象。 

可以使用cin来将键盘输入存储到string对象中。 

可以使用cout来显示string对象。 

可以使用数组表示法来访问存储在string对象中的字符。

```
string str1;
string str2 = "panther";
```

类设计让程序能够自动处理string的大小。例如，str1的声明创建一个长度为0的string对象，但程序将输入读取到str1中时，将自动调整str1的长度：

```
cin >> str1;
```

### C++11字符串初始化

C++11也允许将列表初始化用于C-风格字符串和string对象：

```cpp
char first_date[] = {"Le Chapon Dodu"};
char second_date[] = {"The Elegant Plate"};
string third_date = {"The Bread Bowl"};
string fourth_date = {"hank's Fine Eats"};
```

### 赋值、拼接和附加 

使用string类时，某些操作比使用数组时更简单。例如，不能将一个数组赋给另一个数组，但可以将一个string对象赋给另一个string对 象：

```cpp
char charr1[20];
char charr2[20] = "jaguar";
string str1;
string str2= "panther";
charr1 =charr2; //不允许
str1 = str2;
```

string类简化了字符串合并操作。可以使用运算符+将两个string对象合并起来，还可以使用运算符+=将字符串附加到string对象的末尾。继续前面的代码，您可以这样做： 

```
string str3;
str3 =str1+str2;
str1 += str2;
```

```cpp
// strtype2.cpp �- assigning, adding, and appending
#include <iostream>
#include <string>               // make string class available
int main()
{
    using namespace std;
    string s1 = "penguin";
    string s2, s3;

    cout << "You can assign one string object to another: s2 = s1\n";
    s2 = s1;
    cout << "s1: " << s1 << ", s2: " << s2 << endl;
    cout << "You can assign a C-style string to a string object.\n";
    cout << "s2 = \"buzzard\"\n";
    s2 = "buzzard";
    cout << "s2: " << s2 << endl;
    cout << "You can concatenate strings: s3 = s1 + s2\n";
    s3 = s1 + s2;
    cout << "s3: " << s3 << endl;
    cout << "You can append strings.\n";
    s1 += s2;
    cout <<"s1 += s2 yields s1 = " << s1 << endl;
    s2 += " for a day";
    cout <<"s2 += \" for a day\" yields s2 = " << s2 << endl;

    //cin.get();
    return 0;
}
//You can assign one string object to another: s2 = s1
//s1: penguin, s2: penguin
//You can assign a C-style string to a string object.
//s2 = "buzzard"
//s2: buzzard
//You can concatenate strings: s3 = s1 + s2
//s3: penguinbuzzard
//You can append strings.
//s1 += s2 yields s1 = penguinbuzzard
//s2 += " for a day" yields s2 = buzzard for a day
```

### string类的其他操作 

在C++新增string类之前，程序员也需要完成诸如给字符串赋值等工作。对于C-风格字符串，程序员使用C语言库中的函数来完成这些任 务。头文件cstring（以前为string.h）提供了这些函数。例如，可以使用函数strcpy( )将字符串复制到字符数组中，使用函数strcat( )将字符串附加到字符数组末尾：

```
strcpy(charr1,charr2);//将charr2复制到charr1
strcat(charr1,charr2);//将charr2，拼接到charr1
```

```cpp
// strtype3.cpp -- more string class features
#include <iostream>
#include <string>               // make string class available
#include <cstring>              // C-style string library
int main()
{
    using namespace std;
    char charr1[20];
    char charr2[20] = "jaguar";
    string str1;
    string str2 = "panther";

    // assignment for string objects and character arrays
    str1 = str2;                // copy str2 to str1
    strcpy(charr1, charr2);     // copy charr2 to charr1

    // appending for string objects and character arrays
    str1 += " paste";           // add paste to end of str1
    strcat(charr1, " juice");   // add juice to end of charr1

    // finding the length of a string object and a C-style string
    int len1 = str1.size();     // obtain length of str1
    int len2 = strlen(charr1);  // obtain length of charr1

    cout << "The string " << str1 << " contains "
         << len1 << " characters.\n";
    cout << "The string " << charr1 << " contains "
         << len2 << " characters.\n";
    // cin.get();

    return 0;
}
//The string panther paste contains 13 characters.
//The string jaguar juice contains 12 characters.
```

使用字符数组时，总是存在目标数组过小，无法存储指定信息的危险

```
char site[10] = "house";
strcat(site," of pancakes");;//memory problem
```

函数strcat( )试图将全部12个字符复制到数组site中，这将覆盖相邻的内存。这可能导致程序终止，或者程序继续运行，但数据被损坏。 string类具有自动调整大小的功能，从而能够避免这种问题发生。C函数 库确实提供了与strcat( )和strcpy( )类似的函数—strncat( )和strncpy( )，它 们接受指出目标数组最大允许长度的第三个参数，因此更为安全，但使 用它们进一步增加了编写程序的复杂度

下面是两种确定字符串中字符数的方法：

```
int len1=str1.size();
int len2=strlen(charr1);
```

函数strlen( )是一个常规函数，它接受一个C-风格字符串作为参数， 并返回该字符串包含的字符数。函数size( )的功能基本上与此相同，但句法不同：str1不是被用作函数参数，而是位于函数名之前，它们之间用句点连接

这种句法表明，str1是一个对象，而size( )是一个类方法。方法是一个函数，只能通过其所属类的对象进行调用。在这里，str1是一个string对象，而size( )是string类的一个方法。总之，C函数使用参数来指出要使用哪个字符串，而C++ string类对象使用对象名和句点运算符来指出要使用哪个字符串。

### 4.3.4 string类I/O

```cpp
// strtype4.cpp -- line input
#include <iostream>
#include <string>               // make string class available
#include <cstring>              // C-style string library
int main()
{
    using namespace std;
    char charr[20]; 
    string str;

    cout << "Length of string in charr before input: " 
         << strlen(charr) << endl;//这里的长度是随机的，因为是未初始化，所以length是随机值
    cout << "Length of string in str before input: "
         << str.size() << endl;
    cout << "Enter a line of text:\n";
    cin.getline(charr, 20);     // indicate maximum length
    cout << "You entered: " << charr << endl;
    cout << "Enter another line of text:\n";
    getline(cin, str);          // cin now an argument; no length specifier
    cout << "You entered: " << str << endl;
    cout << "Length of string in charr after input: " 
         << strlen(charr) << endl;
    cout << "Length of string in str after input: "
         << str.size() << endl;
    // cin.get();

    return 0; 
}
//Length of string in charr before input: 1
//Length of string in str before input: 0
//Enter a line of text:
//peanut butter
//You entered: peanut butter
//Enter another line of text:
//blueberry jam
//You entered: blueberry jam
//Length of string in charr after input: 13
//Length of string in str after input: 13
```

该数组的长度要大。这里要两点需要说明。首先，为初始化的数组的内容是未定义的；其次，函数strlen( )从数组的第一个元素开始计算字节 数，直到遇到空字符。在这个例子中，在数组末尾的几个字节后才遇到空字符。对于未被初始化的数据，第一个空字符的出现位置是随机的，因此您在运行该程序时，得到的数组长度很可能与此不同。另外，用户输入之前，str中的字符串长度为0。这是因为未被初始 化的string对象的长度被自动设置为0。

下面是将一行输入读取到数组中的代码：

```
cin.getline(charr,20);
```

这种句点表示法表明，函数getline( )是istream类的一个类方法（还记得吗，cin是一个istream对象）。正如前面指出的，第一个参数是目标数组；第二个参数数组长度，getline( )使用它来避免超越数组的边界。

下面是将一行输入读取到string对象中的代码：

```
getline(cin,str);
```

这里没有使用句点表示法，这表明这个getline( )不是类方法。它将cin作为参数，指出到哪里去查找输入。另外，也没有指出字符串长度 的参数，因为string对象将根据字符串的长度自动调整自己的大小。

那么，为何一个getline( )是istream的类方法，而另一个不是呢？在引入string类之前很久，C++就有istream类。因此istream的设计考虑到了诸如double和int等基本C++数据类型，但没有考虑string类型，所以 istream类中，有处理double、int和其他基本类型的类方法，但没有处理string对象的类方法。

除char类型外，C++还有类型wchar_t；而C++11新增了类型char16_t和char32_t。可创建这些类型的数组和这些类型的字符串字面值。对于这些类型的字符串字面值，C++分别使用前缀L、u和U表示，下面是一个如何使用这些前缀的例子： 

```
wchar_t title[] = L"Chief Asreogator";
char16_t name[] = u"Felonia Ripova";
char32_t car[] = U"humber super sniper";
```

C++11还支持Unicode字符编码方案UTF-8。在这种方案中，根据编码的数字值，字符可能存储为1～4个八位组。C++使用前缀u8来表示这种类型的字符串字面值。

C++11新增的另一种类型是原始（raw）字符串。在原始字符串中，字符表示的就是自己，例如，序列\n不表示换行符，而表示两个常 规字符—斜杠和n，因此在屏幕上显示时，将显示这两个字符。另一个 例子是，可在字符串中使用"，而无需使用繁琐的 "。当然，既然可在字符串字面量包含"，就不能再使用它来表示字符串 的开头和末尾。因此，原始字符串将"(和)"用作定界符，并使用前缀R来 标识原始字符串：

```cpp
cout << R "(jim "king" Tutt uses "\n" instead of endl.)" << "\n"
```

## 结构简介

结构是 一种比数组更灵活的数据格式，因为同一个结构可以存储多种类型的数据，这使得能够将有关篮球运动员的信息放在一个结构中，从而将数据的表示合并到一起

结构是用户定义的类型，而结构声明定义了这种类型的数据属性。 定义了类型后，便可以创建这种类型的变量。因此创建结构包括两步。 首先，定义结构描述—它描述并标记了能够存储在结构中的各种数据类型。然后按描述创建结构变量（结构数据对象）。

```
struct inflatable {
	char name[20];
	float volume;
	double price;
}
```

关键字struct表明，这些代码定义的是一个结构的布局。标识符 inflatable是这种数据格式的名称，因此新类型的名称为inflatable。这 样，便可以像创建char或int类型的变量那样创建inflatable类型的变量 了。接下来的大括号中包含的是结构存储的数据类型的列表，其中每个列表项都是一条声明语句。这个例子使用了一个适合用于存储字符串的 char数组、一个float和一个double。列表中的每一项都被称为结构成员，因此infatable结构有3个成员（参见图4.6）。总之，结构定义指出了新类型（这里是inflatable）的特征。 

定义结构后，便可以创建这种类型的变量了：

```
inflatable hat;
inflatable woopie_cushion;
inflatable mainframe;
```

如果您熟悉C语言中的结构，则可能已经注意到了，C++允许在声明结构变量时省略关键字struct：

```
struct inflatable goose;  //keyword struct required in C
inflatable vincent; //keyword struct not require c++;
```

由于hat的类型为inflatable，因此可以使用成员运算符（.）来访问各个成员。例如，hat.volume指的是结构的volume成员，hat.price指的是 price成员。同样，vincent.price是vincent变量的price成员。总之，通过成员名能够访问结构的成员，就像通过索引能够访问数组的元素一样。由于price成员被声明为double类型，因此hat.price和vincent.price相当于是double类型的变量，可以像使用常规double变量那样来使用它们。总之，hat是一个结构，而hat.price是一个double变量。顺便说一句，访问类成员函数（如cin.getline( )）的方式是从访问结构成员变量（如vincent.price）的方式衍生而来的。

### 在程序中使用结构

```CPP
// structur.cpp -- a simple structure
#include <iostream>
struct inflatable   // structure declaration
{
    char name[20];
    float volume;
    double price;
};

int main()
{
    using namespace std;
    inflatable guest =
            {
                    "Glorious Gloria",  // name value
                    1.88,               // volume value
                    29.99               // price value
            };  // guest is a structure variable of type inflatable
// It's initialized to the indicated values
    inflatable pal =
            {
                    "Audacious Arthur",
                    3.12,
                    32.99
            };  // pal is a second variable of type inflatable
// NOTE: some implementations require using
// static inflatable guest =

    cout << "Expand your guest list with " << guest.name;
    cout << " and " << pal.name << "!\n";
// pal.name is the name member of the pal variable
    cout << "You can have both for $";
    cout << guest.price + pal.price << "!\n";
    // cin.get();
    return 0;
}
//Expand your guest list with Glorious Gloria and Audacious Arthur!
//You can have both for $62.98!
```

结构有两种申明方式：可以 将声明放在main( )函数中，紧跟在开始括号的后面。另一种选择是将声 明放到main( )的前面，这里采用的便是这种方式，位于函数外面的声明 被称为外部声明。对于这个程序来说，两种选择之间没有实际区别。但 是对于那些包含两个或更多函数的程序来说，差别很大。外部声明可以 被其后面的任何函数使用，而内部声明只能被该声明所属的函数使用。 通常应使用外部声明，这样所有函数都可以使用这种类型的结构。c++提倡使用外部结构声明

### C++11结构初始化 

与数组一样，C++11也支持将列表初始化用于结构，且等号（=） 是可选的：

```
inflatable duck {"Daphne",0.12,9.98}
```

其次，如果大括号内未包含任何东西，各个成员都将被设置为零。最后，不允许缩窄转换。

 

### 结构可以将**string**类作为成员吗

```cpp
#include <string>
struct inflatable {
	std::string name;
	float volume;
	double price;
}
```

一定要让结构定义能够访问名称空间std。为此，可以将编译指令 using移到结构定义之前；也可以像前面那样，将name的类型声明为 std::string。

### 其他结构属性

可以同时完成定义结构和创建结构变量的工作。为此，只需将变量名放在结束括号的后面即可： 

```cpp
Struct perks {
	int key_number;
	char car[12]
} mr_smith,ms_jones;
```

甚至可以初始化以这种方式创建的变量

```
struct perks {
	int key_number;
	char car[2];
} mr_glitz = {
	7,
	"Packard"
}
```

然而，将结构定义和变量声明分开，可以使程序更易于阅读和理解

还可以声明没有名称的结构类型，方法是省略名称，同时定义一种结构类型和一个这种类型的变量：

```
struct {
	int x;
	int y;
} position
```

这样将创建一个名为position的结构变量。可以使用成员运算符来访问它的成员（如position.x），但这种类型没有名称，因此以后无法创建这种类型的变量

### 结构数组

```cpp
// arrstruc.cpp -- an array of structures
#include <iostream>
struct inflatable
{
    char name[20];
    float volume;
    double price;
};
int main()
{
    using namespace std;
    inflatable guests[2] =          // initializing an array of structs
    {
        {"Bambi", 0.5, 21.99},      // first structure in array
        {"Godzilla", 2000, 565.99}  // next structure in array
    };

    cout << "The guests " << guests[0].name << " and " << guests[1].name
         << "\nhave a combined volume of "
         << guests[0].volume + guests[1].volume << " cubic feet.\n";
    // cin.get();
    return 0; 
}
```

### 结构中的位字段

与C语言一样，C++也允许指定占用特定位数的结构成员，这使得创建与某个硬件设备上的寄存器对应的数据结构非常方便。字段的类型 应为整型或枚举，接下来是冒号，冒号后面是一个数字，它指定了使用的位数。可以使用没有名称的字段来提供间距。每个 成员都被称为位字段（bit field）。下面是一个例子：

```
struct torgle_register {
	unsigned int SN : 4 //4 bits for SN value
	unsigned int : 4 // 4 bits unused
	bool goodIn : 1; //valid input (1bit)
	bool goodTorgle : 1; // successful torgling
}
```

可以像通常那样初始化这些字段，还可以使用标准的结构表示法来访问位字段：

```cpp
torgle_register tr {14,true,false};
if(tr.goodIn){
  ....
}
```

位字段通常用在低级编程中。

## 共用体

共用体（union）是一种数据格式，它能够存储不同的数据类型，但只能同时存储其中的一种类型。也就是说，结构可以同时存储int、 long和double，共用体只能存储int、long或double。共用体的句法与结构相似，但含义不同。例如，请看下面的声明：

```
union one4all {
	int int_val;
	long long_val;
	double double_val;
}
```

可以使用one4all变量来存储int、long或double，条件是在不同的时间进行：

```
one4all pail;
pail.int_val = 15;
cout<< pail.int_val;
pail.double_val =1.38;
cout << pail.double_val;
```

因此，pail有时可以是int变量，而有时又可以是double变量。成员 名称标识了变量的容量。由于共用体每次只能存储一个值，因此它必须有足够的空间来存储最大的成员，所以，共用体的长度为其最大成员的长度。共用体的用途之一是，当数据项使用两种或更多种格式（但不会同时使用）时，可节省空间。例如，假设管理一个小商品目录，其中有一些商品的ID为整数，而另一些的ID为字符串。在这种情况下，可以这样做：

```
struct widget{
	char brand[20];
	int type;
	union id
	{
		long id_num;
		char id_char[20];
	} id_val;
};
```

匿名共用体（anonymous union）没有名称，其成员将成为位于相同地址处的变量。显然，每次只有一个成员是当前的成员：

```cpp
struct widget {
	char brand[20];
	int type;
	union
	{
		long id_num;
		char id_char[20];
	}
}

if(price.type==1)
	cin >> price.id_num;
else
	cin >> price.id_char;
```

由于共用体是匿名的，因此id_num和id_char被视为prize的两个成员，它们的地址相同，所以不需要中间标识符id_val。程序员负责确定 当前哪个成员是活动的。

共用体常用于（但并非只能用于）节省内存。当前，系统的内存多达数GB甚至数TB，好像没有必要节省内存，但并非所有的C++程序都 是为这样的系统编写的。C++还用于嵌入式系统编程，如控制烤箱、 MP3播放器或火星漫步者的处理器。对这些应用程序来说，内存可能非常宝贵。另外，共用体常用于操作系统数据结构或硬件数据结构

## 枚举

C++的enum工具提供了另一种创建符号常量的方式，这种方式可以代替const。它还允许定义新类型，但必须按严格的限制进行。使用 enum的句法与使用结构相似。例如，请看下面的语句： 

```cpp
enum spectum {red,orange,yellow,green,blue,violet,indigo,ultraviolet};
```

这条语句完成两项工作。 

让spectrum成为新类型的名称；spectrum被称为枚举（enumeration），就像struct变量被称为结构一样。将red、orange、yellow等作为符号常量，它们对应整数值0～7。这些常量叫作枚举量（enumerator）。

在默认情况下，将整数值赋给枚举量，第一个枚举量的值为0，第二个枚举量的值为1，依次类推。可以通过显式地指定整数值来覆盖默 认值

可以用枚举名来声明这种类型的变量

```
spectrum band;
```

在不进行强制类型转换的情况下，只能将定义枚举时使用的枚举量赋给这种枚举的变量

```
band=blue;
```

因此，spectrum变量受到限制，只有8个可能的值。如果试图将一个非法值赋给它，则有些编译器将出现编译器错误，而另一些则发出警 告。为获得最大限度的可移植性，应将把非enum值赋给enum变量视为错误

```
band =orange; //valid
++band;   //not valid ,
band = orange + red; //not valid
```

然而，有些实现并没有这种限制，这有可能导致违反类型限制。例 如，如果band的值为ultraviolet（7），则++band（如果有效的话）将把band增加到8，而对于spectrum类型来说，8是无效的。另外，为获得最大限度的可移植性，应采纳较严格的限制

枚举量是整型，可被提升为int类型，但int类型不能自动转换为枚举类型：

```
int color =blue //valid
band =3//valid but 	 int not convert to spectrum
color = 3+red;
```

虽然在这个例子中，3对应的枚举量是green，但将3赋给band将导致类型错误。不过将green赋给band是可以的，因为它们都是spectrum类型。同样，有些实现方法没有这种限制。表达式3 + red中的加法并非为枚举量定义，但red被转换为int类型，因此结果的类型也是int。由于在这种情况下，枚举将被转换为int，因此可以在算术表达式中同时使用枚举和常规整数，尽管并没有为枚举本身定义算术运算.

```
band =orange + red;
```

非法的原因有些复杂。确实没有为枚举定义运算符+，但用于算术表达式中时，枚举将被转换为整数，因此表达式orange + red将被转换为1 + 0。这是一个合法的表达式，但其类型为int，不能将其赋给类型为spectrum的变量band。 

如果int值是有效的，则可以通过强制类型转换，将它赋给枚举变量：

```
band = spectrum(3);  //typecast 3 to type spectrum
band = spectrum(40003);  //undefined 不要对不确定的值进行强制转换
```

### 设置枚举量的值

可以使用赋值运算符来显式地设置枚举量的值

```
enum bits{one =1,two=2,four=4,eight=8};
```

指定的值必须是整数。也可以只显式地定义其中一些枚举量的值

```
enum bigstep{first,second=100,third};
```

这里，first在默认值是0，后面没有被初始化的枚举量的值将比其前面的枚举量大1。因此，third的值为101。最后，可以创建多个值相同的枚举量： 

```
enum {zero,null = 0,one,numero_uno = 1};
```

其中，zero和null都为0，one和umero_uno都为1。在C++早期的版本中，只能将int值（或提升为int的值）赋给枚举量，但这种限制取消了，因此可以使用long甚至long long类型的值

### 枚举的取值范围

最初，对于枚举来说，只有声明中指出的那些值是有效的。然而，C++现在通过强制类型转换，增加了可赋给枚举变量的合法值。每个枚 举都有取值范围（range），通过强制类型转换，可以将取值范围中的任何整数值赋给枚举变量，即使这个值不是枚举值

```
enum bits{one = 1,two = 2,four = 4,eight = 8};
myflag=bits(6)  //在这个范围内都是有效的 6不是枚举值，但它位于枚举定义的取值范围内
```

取值范围的定义如下。首先，要找出上限，需要知道枚举量的最大值。找到大于这个最大值的、最小的2的幂，将它减去1，得到的便是取 值范围的上限。例如，前面定义的bigstep的最大值枚举值是101。在2的幂中，比这个数大的最小值为128，因此取值范围的上限为127。要计算下限，需要知道枚举量的最小值。如果它不小于0，则取值范围的下限为0；否则，采用与寻找上限方式相同的方式，但加上负号。例如，如果最小的枚举量为−6，而比它小的、最大的2的幂是−8（加上负号），因此下限为−7。

选择用多少空间来存储枚举由编译器决定。对于取值范围较小的枚 举，使用一个字节或更少的空间；而对于包含long类型值的枚举，则使用4个字节。

C++11扩展了枚举，增加了作用域内枚举

## 指针和自由存储空间

在第3章的开头，提到了计算机程序在存储数据时必须跟踪的3种基 本属性

信息存储在何处；

存储的值为多少；

存储的信息是什么类型；

您使用过一种策略来达到上述目的：定义一个简单变量。声明语句指出了值的类型和符号名，还让程序为值分配内存，并在内部跟踪该内 存单元。

下面来看一看另一种策略，它在开发C++类时非常重要。这种策略以指针为基础，指针是一个变量，其存储的是值的地址，而不是值本 身。在讨论指针之前，我们先看一看如何找到常规变量的地址。只需对变量应用地址运算符（&），就可以获得它的位置；例如，如果home是一个变量，则&home是它的地址。

```cpp
// address.cpp -- using the & operator to find addresses
#include <iostream>
int main()
{
    using namespace std;
    int donuts = 6;
    double cups = 4.5;

    cout << "donuts value = " << donuts;
    cout << " and donuts address = " << &donuts << endl;
// NOTE: you may need to use unsigned (&donuts)
// and unsigned (&cups)
    cout << "cups value = " << cups;
    cout << " and cups address = " << &cups << endl;
    // cin.get();
    return 0;
}
//donuts value = 6 and donuts address = 0x16fadf738
//cups value = 4.5 and cups address = 0x16fadf730
```

显示地址时，该实现的cout使用十六进制表示法，因为这是常用于描述内存的表示法（有些实现可能使用十进制表示法）。在该实现中， donuts的存储位置比cups要低。两个地址的差为0x0065fd44 –0x0065fd40（即4）。这是有意义的，因为donuts的类型为int，而这种类 型使用4个字节。当然，不同系统给定的地址值可能不同。有些系统可能先存储cups，再存储donuts，这样两个地址值的差将为8个字节，因为 cups的类型为double。另外，在有些系统中，可能不会将这两个变量存储在相邻的内存单元中。 

使用常规变量时，值是指定的量，而地址为派生量

面向对象编程与传统的过程性编程的区别在于，OOP强调的是在运行阶段（而不是编译阶段）进行决策。运行阶段指的是程序正在运行时，编译阶段指的是编译器将程序组合起来时。运行阶段决策就好比度假时，选择参观哪些景点取决于天气和当时的心情；而编译阶段 决策更像不管在什么条件下，都坚持预先设定的日程安排。 

运行阶段决策提供了灵活性，可以根据当时的情况进行调整。例如，考虑为数组分配内 存的情况。传统的方法是声明一个数组。要在C++中声明数组，必须指定数组的长度。因此，数组长度在程序编译时就设定好了；这就是编译阶段决策。您可能认为，在80%的情况下，一 个包含20个元素的数组足够了，但程序有时需要处理200个元素。为了安全起见，使用了一个 包含200个元素的数组。这样，程序在大多数情况下都浪费了内存。OOP通过将这样的决策推 迟到运行阶段进行，使程序更灵活。在程序运行后，可以这次告诉它只需要20个元素，而还可以下次告诉它需要205个元素

总之，使用OOP时，您可能在运行阶段确定数组的长度。为使用这种方法，语言必须允许在程序运行时创建数组。稍后您看会到，C++采用的方法是，使用关键字new请求正确数量的内存以及使用指针来跟踪新分配的内存的位置。 

在运行阶段做决策并非OOP独有的，但使用C++编写这样的代码比使用C语言简单处理存储数据的新策略刚好相反，将地址视为指定的量，而将值视为派生量。一种特殊类型的变量—指针用于存储值的地址。因此，指针名表示的是地址。*运算符被称为间接值（indirect velue）或解除引用 （dereferencing）运算符，将其应用于指针，可以得到该地址处存储的 值（这和乘法使用的符号相同；C++根据上下文来确定所指的是乘法还是解除引用）。例如，假设manly是一个指针，则manly表示的是一个地址，而*manly表示存储在该地址处的值。*manly与常规int变量等效

```cpp
#include <iostream>
int main()
{
    using namespace std;
    int updates = 6;        // declare a variable
    int * p_updates;        // declare pointer to an int

    p_updates = &updates;   // assign address of int to pointer

// express values two ways
    cout << "Values: updates = " << updates;
    cout << ", *p_updates = " << *p_updates << endl;

// express address two ways
    cout << "Addresses: &updates = " << &updates;
    cout << ", p_updates = " << p_updates << endl;

// use pointer to change value
    *p_updates = *p_updates + 1;
    cout << "Now updates = " << updates << endl;
    // cin.get();
    return 0;
}
//Values: updates = 6, *p_updates = 6
//Addresses: &updates = 0x16dd2b738, p_updates = 0x16dd2b738
//Now updates = 7
```

从中可知，int变量updates和指针变量p_updates只不过是同一枚硬币的两面。变量updates表示值，并使用&运算符来获得地址；而变量p_updates表示地址，并使用*运算符来获得值。由于 p_updates指向updates，因此*p_updates和updates完全等价。可以像使用 int变量那样使用*p_updates。正如程序清单4.15表明的，甚至可以将值赋给*p_updates。这样做将修改指向的值，即updates。

### 声明和初始化指针

指针声明必须指定指针指向的数据的类型

```cpp
int * p_updates;
```

这表明，* p_updates的类型为int。由于*运算符被用于指针，因此p_updates变量本身必须是指针。我们说p_updates指向int类型，我们还说p_updates的类型是指向int的指针，或int*。可以这样说，p_updates是指针（地址），而*p_updates是int，而不是指针

顺便说一句，*运算符两边的空格是可选的。传统上，C程序员使用这种格式：

```
int *ptr
```

这强调*ptr是一个int类型的值。而很多C++程序员使用这种格式：

```
inr* ptr;
```

这强调的是：int*是一种类型—指向int的指针。在哪里添加空格对于编译器来说没有任何区别，您甚至可以这样做：

```
int*ptr;
```

但要知道的是，下面的声明创建一个指针（p1）和一个int变量（p2）：

```
int* p1, p2;
```

对每个指针变量名，都需要使用一个*。

**在C++中，int *是一种复合类型，是指向int的指针**

可以用同样的句法来声明指向其他类型的指针：

```
double * tax_ptr;
char * str;
```

由于已将tax_ptr声明为一个指向double的指针，因此编译器知道 tax_ptr是一个double类型的值。也就是说，它知道*tax_ptr是一个以浮 点格式存储的值，这个值（在大多数系统上）占据8个字节。指针变量 不仅仅是指针，而且是指向特定类型的指针。tax_ptr的类型是指向 double的指针（或double *类型），str是指向char的指针类型（或char *）。尽管它们都是指针，却是不同类型的指针。和数组一样，指针都 是基于其他类型的。 

虽然tax_ptr和str指向两种长度不同的数据类型，但这两个变量本身的长度通常是相同的。也就是说，char的地址与double的地址的长度相 同，地址的长度或值既不能指示关于变量的长度或类型的任何信息，也不能指示该地址上有什么建筑物。一般来说，地址需要2个还是4 个字节，取决于计算机系统

可以在声明语句中初始化指针。在这种情况下，被初始化的是指针，而不是它指向的值。也就是说，下面的语句将pt（而不是*pt）的值 设置为&higgens：

```
int higgens = 5;
int * pt=&higgens;
```

```cpp
// init_ptr.cpp -- initialize a pointer
#include <iostream>
int main()
{
    using namespace std;
    int higgens = 5;
    int * pt = &higgens;

    cout << "Value of higgens = " << higgens
         << "; Address of higgens = " << &higgens << endl;
    cout << "Value of *pt = " << *pt
         << "; Value of pt = " << pt << endl;
    // cin.get();
    return 0;
}
//Value of higgens = 5; Address of higgens = 0x16fc77738
//Value of *pt = 5; Value of pt = 0x16fc77738	
```

 

### 指针的危险

在C++中创建指针时，计算机将分配用来存储地址的内存，但不会分配用来存储指针所指向的数据的内存

```
long * fellow;
*fellow = 23333;
```

fellow确实是一个指针，但它指向哪里呢？上述代码没有将地址赋 给fellow。那么223323将被放在哪里呢？我们不知道。由于fellow没有被 初始化，它可能有任何值。不管值是什么，程序都将它解释为存储 223323的地址。如果fellow的值碰巧为1200，计算机将把数据放在地址 1200上，即使这恰巧是程序代码的地址。fellow指向的地方很可能并不 是所要存储223323的地方。这种错误可能会导致一些最隐匿、最难以跟 踪的bug

一定要在对指针应用解除引用运算符（*）之前，将指针初始化为一个确定的、适当的地址。这是关于使用指针的金科玉律。

 

### 指针和数字

指针不是整型，虽然计算机通常把地址当作整数来处理。从概念上 看，指针与整数是截然不同的类型。整数是可以执行加、减、除等运算 的数字，而指针描述的是位置，将两个地址相乘没有任何意义。从可以 对整数和指针执行的操作上看，它们也是彼此不同的。因此，不能简单 地将整数赋给指针

```
int * pt;
pt = 0xB800000;//左边是指向int型指针，但是右边是一个整数
pt = （int *）0xB800000;//要强制转成int * 才可以；这样两边都是int 指针类型
```

### 使用new来分配内存 

前面我们都将指针初始化为变量的地址；变量是在编译时分配的有名称的内存，而指针只是为可以通过名称直接访问的内存提供了 一个别名。指针真正的用武之地在于，在运行阶段分配未命名的内存以存储值。在这种情况下，只能通过指针来访问内存。在C语言中，可以 用库函数malloc( )来分配内存；在C++中仍然可以这样做，但C++还有更好的方法—new运算符。 

程序员要告诉new，需要为哪种数据类型分配内存；new将找到一个长度正确的内存块，并返回该内存块的地址。程序员的责任是将该地址赋给一个指针

```cpp
int * pn =new int；
```

new int告诉程序，需要适合存储int的内存。new运算符根据类型来 确定需要多少字节的内存。然后，它找到这样的内存，并返回其地址。接下来，将地址赋给pn，pn是被声明为指向int的指针。现在，pn是地址，而*pn是存储在那里的值。将这种方法与将变量的地址赋给指针进行比较：

```cpp
int higgens;
int * pt = &higgens;
```

在这两种情况（pn和pt）下，都是将一个int变量的地址赋给了指针。在第二种情况下，可以通过名称higgens来访问该int，在第一种情况 下，则只能通过该指针进行访问。这引出了一个问题：pn指向的内存没有名称，如何称呼它呢？我们说pn指向一个数据对象，这里的“对象”不是“面向对象编程”中的对象，而是一样“东西”。术语“数据对象”比“变 量”更通用，它指的是为数据项分配的内存块。因此，变量也是数据对象，但pn指向的内存不是变量。乍一看，处理数据对象的指针方法可能不太好用，但它使程序在管理内存方面有更大的控制权。

为一个数据对象（可以是结构，也可以是基本类型）获得并指定分配内存的通用格式如下

```cpp
typeName * pointer_name = new typeName;
```

需要在两个地方指定数据类型：用来指定需要什么样的内存和用来声明合适的指针。当然，如果已经声明了相应类型的指针，则可以使用 该指针，而不用再声明一个新的指针

```cpp
// use_new.cpp -- using the new operator
#include <iostream>
int main()
{
    using namespace std;
    int nights = 1001;
    int * pt = new int;         // allocate space for an int
    *pt = 1001;                 // store a value there

    cout << "nights value = ";
    cout << nights << ": location " << &nights << endl;
    cout << "int ";
    cout << "value = " << *pt << ": location = " << pt << endl;

    double * pd = new double;   // allocate space for a double
    *pd = 10000001.0;           // store a double there

    cout << "double ";
    cout << "value = " << *pd << ": location = " << pd << endl;
    cout << "location of pointer pd: " << &pd << endl;
    cout << "size of pt = " << sizeof(pt);
    cout << ": size of *pt = " << sizeof(*pt) << endl;
    cout << "size of pd = " << sizeof pd;
    cout << ": size of *pd = " << sizeof(*pd) << endl;
    // cin.get();
    return 0;
}
//nights value = 1001: location 0x16b21f738
//int value = 1001: location = 0x600003ed0030
//double value = 1e+07: location = 0x600003ed0040
//location of pointer pd: 0x16b21f728
//size of pt = 8: size of *pt = 4
//size of pd = 8: size of *pd = 8
```

对于指针，需要指出的另一点是，new分配的内存块通常与常规变量声明分配的内存块不同。变量nights和pd的值都存储在被称为栈 （stack）的内存区域中，而new从被称为堆heap）或自由存储区（free store）的内存区域分配内存。

### 使用delete释放内存 

当需要内存时，可以使用new来请求，这只是C++内存管理数据包中有魅力的一个方面。另一个方面是delete运算符，它使得在使用完内 存后，能够将其归还给内存池，这是通向最有效地使用内存的关键一步。归还或释放（free）的内存可供程序的其他部分使用。使用delete时，后面要加上指向内存块的指针（这些内存块最初是用new分配的）：

```
int *ps=new int;
delete ps;
```

这将释放ps指向的内存，但不会删除指针ps本身。例如，可以将ps重新指向另一个新分配的内存块。一定要配对地使用new和delete；否则将发生内存泄漏（memory leak），也就是说，被分配的内存再也无法使用了。如果内存泄漏严重，则程序将由于不断寻找更多内存而终止。 

不要尝试释放已经释放的内存块，C++标准指出，这样做的结果将是不确定的，这意味着什么情况都可能发生。另外，不能使用delete来 释放声明变量所获得的内存：

```cpp
int * ps = new int; //ok
delete ps; //ok
delete ps; //not ok 不要尝试释放已经释放的内存块
int jud = 5; //ok
int * pi =&jud; //ok
delete pi //not allowed new和delete需要配合使用，不能使用delete删除非new创建的指针赋值
```

**只能用delete来释放使用new分配的内存。然而，对空指针使用delete是安全的**

注意，使用delete的关键在于，将它用于new分配的内存。这并不意味着要使用用于new的指针，而是用于new的地址：

```cpp
int * ps = new int; //allocate memory
int * pq = ps; //set same pointer to same block
delete pq; //delete second block	
```

一般来说，不要创建两个指向同一个内存块的指针，因为这将增加错误地删除同一个内存块两次的可能性。但稍后您会看到，对于返回指 针的函数，使用另一个指针确实有道理

### 使用new来创建动态数组

在编译时给数组分配内存被称 为静态联编（static binding），意味着数组是在编译时加入到程序中的。但使用new时，如果在运行阶段需要数组，则创建它；如果不需要，则不创建。还可以在程序运行时选择数组的长度。这被称为动态联 编（dynamic binding），意味着数组是在程序运行时创建的。这种数组 叫作动态数组（dynamic array）。使用静态联编时，必须在编写程序时指定数组的长度；使用动态联编时，程序将在运行时确定数组的长度。

#### 使用new创建动态数组

在C++中，创建动态数组很容易；只要将数组的元素类型和元素数目告诉new即可。必须在类型名后加上方括号，其中包含元素数目。例 如，要创建一个包含10个int元素的数组，可以这样做：

```cpp
int * arr =new int[10];//new运算符返回第一个元素的地址
```

当程序使用完new分配的内存块时，应使用delete释放它们。然而，对于使用new创建的数组，应使用另一种格式的delete来释放： 

```cpp
delete [] arr;
```

方括号告诉程序，应释放整个数组，而不仅仅是指针指向的元素。 请注意delete和指针之间的方括号。如果使用new时，不带方括号，则使 用delete时，也不应带方括号。如果使用new时带方括号，则使用delete 时也应带方括号。C++的早期版本无法识别方括号表示法。然而，对于ANSI/ISO标准来说，new与delete的格式不匹配导致的后果是不确定的，这意味着程序员不能依赖于某种特定的行为。

总之，使用new和delete时，应遵守以下规则:

- 不要使用delete来释放不是new分配的内存。 
- 不要使用delete释放同一个内存块两次。 
- 如果使用new [ ]为数组分配内存，则应使用delete [ ]来释放。 
- 如果使用new 为一个实体分配内存，则应使用delete（没有方括号）来释放
- 对空指针应用delete是安全的。

Arr是指向一个int（数组第一个元素）的指针。您的责任是跟踪内存块中的元素个数。也就是说，由于编译器不能对psome是指向10个整数中的第1个这种情况进行跟踪，因此编写程序时，必须让程序跟踪元素的数目。

实际上，程序确实跟踪了分配的内存量，以便以后使用delete [ ]运算符时能够正确地释放这些内存。但这种信息不是公用的，例如，不能 使用sizeof运算符来确定动态分配的数组包含的字节数

为数组分配内存的通用格式如下： 

```
typename * pointer_name = new typename[num_elements];
```

使用new运算符可以确保内存块足以存储num_elements个类型为type_name的元素，而pointer_name将指向第1个元素。下面将会看到，可以以使用数组名的方式来使用pointer_name。 

#### 使用动态数组

```
int * psome = new int[10];
```

```cpp
// arraynew.cpp -- using the new operator for arrays
#include <iostream>
int main()
{
    using namespace std;
    double * p3 = new double [3]; // space for 3 doubles
    p3[0] = 0.2;                  // treat p3 like an array name
    p3[1] = 0.5;
    p3[2] = 0.8;
    cout << "p3[1] is " << p3[1] << ".\n";
    p3 = p3 + 1;                  // increment the pointer
    cout << "Now p3[0] is " << p3[0] << " and ";
    cout << "p3[1] is " << p3[1] << ".\n";
    p3 = p3 - 1;                  // point back to beginning
    delete [] p3;                 // free the memory
    // cin.get();
    return 0;
}
//p3[1] is 0.5.
//Now p3[0] is 0.5 and p3[1] is 0.8.
```

## 指针、数组和指针算术

指针和数组基本等价的原因在于指针算术（pointer arithmetic）和C++内部处理数组的方式。

首先，我们来看一看算术。将整数变量加1后，其值将增加1；但将指针变量加1后，增加的量等于它指向的类型的字节数。将指向double的指针加1后，如果系统对double使用8个字节存储，则数值将增加8；将指向short的指针加1后，如果系统对short使用2个字节存储，则指针值将增加2

```cpp
// addpntrs.cpp -- pointer addition
#include <iostream>
int main()
{
    using namespace std;
    double wages[3] = {10000.0, 20000.0, 30000.0};
    short stacks[3] = {3, 2, 1};

// Here are two ways to get the address of an array
    double * pw = wages;     // name of an array = address
    short * ps = &stacks[0]; // or use address operator
// with array element
    cout << "pw = " << pw << ", *pw = " << *pw << endl;
    pw = pw + 1;
    cout << "add 1 to the pw pointer:\n";
    cout << "pw = " << pw << ", *pw = " << *pw << "\n\n";

    cout << "ps = " << ps << ", *ps = " << *ps << endl;
    ps = ps + 1;
    cout << "add 1 to the ps pointer:\n";
    cout << "ps = " << ps << ", *ps = " << *ps << "\n\n";

    cout << "access two elements with array notation\n";
    cout << "stacks[0] = " << stacks[0]
         << ", stacks[1] = " << stacks[1] << endl;
    cout << "access two elements with pointer notation\n";
    cout << "*stacks = " << *stacks
         << ", *(stacks + 1) =  " << *(stacks + 1) << endl;

    cout << sizeof(wages) << " = size of wages array\n";
    cout << sizeof(pw) << " = size of pw pointer\n";
    // cin.get();
    return 0;
}
//pw = 0x16cf37720, *pw = 10000
//add 1 to the pw pointer:
//pw = 0x16cf37728, *pw = 20000
//
//ps = 0x16cf37714, *ps = 3
//add 1 to the ps pointer:
//ps = 0x16cf37716, *ps = 2
//
//access two elements with array notation
//stacks[0] = 3, stacks[1] = 2
//access two elements with pointer notation
//*stacks = 3, *(stacks + 1) =  2
//24 = size of wages array
//8 = size of pw pointer
```

### 程序说明 

将指针变量加1后，其增加的值等于指向的类型占用的字节数。

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-18%2015.38.18.png)

此后，程序对ps执行相同的操作。这一次由于ps指向的是shor t类型，而short占用2个字节，因此将指针加1时，其值将增加2。结果是， 指针也指向数组中下一个元素。

数组表达式stacks[1]。C++编译器将该表达式看作是 *（stacks + 1），这意味着先计算数组第2个元素的地址，然后找到存储 在那里的值。最后的结果便是stacks [1]的含义（运算符优先级要求使用括号，如果不使用括号，将给*stacks加1，而不是给stacks加1）。

从该程序的输出可知，*（stacks + 1）和stacks[1]是等价的。同样， *（stacks + 2）和stacks[2]也是等价的。通常，使用数组表示法时， C++都执行下面的转换

```
arrayname[i] become *(arrayname+1)
```

如果使用的是指针，而不是数组名，则C++也将执行同样的转换： 

```
pointername[i] become *(pintername+i)
```

因此，在很多情况下，可以相同的方式使用指针名和数组名。对于 它们，可以使用数组方括号表示法，也可以使用解除引用运算符 

（*）。在多数表达式中，它们都表示地址。区别之一是，可以修改指 针的值，而数组名是常量：

```
pintername =pointername+1; //valid
arratname = arrayname +1 //no valid
```

另一个区别是，对数组应用sizeof运算符得到的是数组的长度，而 对指针应用sizeof得到的是指针的长度

数组名被解释为其第一个元素的地址，而对数组名应用地址运算符时，得到的是整个数组的地址： 

```
short tell[10];
cout << tell << endl;//display tell[0]
cout << &tell << endl; //	display address of the whole array
```

从数字上说，这两个地址相同；但从概念上说，&tell[0]（即tell）是一个2字节内存块的地址，而&tell是一个20字节内存块的地址。因此，表达式tell + 1将地址值加2，而表达式&tell 2将地址加20。换句话说，tell是一个short指针（\* short），而&tell是一个这样的指针，即指 向包含20个元素的short数组（short (*) [20]）。

```
short (*pas)[20] = &tell;
```

如果省略括号，优先级规则将使得pas先与[20]结合，导致pas是一个short指针数组，它包含20个元素，因此括号是必不可少的。其次，如果要描述变量的类型，可将声明中的变量名删除。因此，pas的类型为short (*) [20]。另外，由于pas被设置为&tell，因此*pas与tell等价，所以(*pas) [0]为tell数组的第一个元素。 

**总之，使用new来创建数组以及使用指针来访问不同的元素很简 单。只要把指针当作数组名对待即可。然而，要理解为何可以这样做， 将是一种挑战。要想真正了解数组和指针，应认真复习它们的相互关 系**

### 指针小结 

#### 声明指针

​	要声明指向特定类型的指针，请使用下面的格式

```
typename * pintername
```

#### 给指针赋值

应将内存地址赋给指针。可以对变量名应用&运算符，来获得被命名的内存的地址，new运算符返回未命名的内存的地址。

```
double * pn;
double * pa;
char * pc;
double bubble = 3.2;
pn = &bubble;
pc = new char;
pa = new double[20];
```

#### 对指针解除引用

对指针解除引用意味着获得指针指向的值。对指针应用解除引用或间接值运算符（*）来解除引用

#### 区分指针和指针所指向的值

如果pt是指向int的指针，则*pt不是指向int的指针，而是完全等同于一个int类型的变量。pt才是指针。 

```
int *pt = new int;//assign an address to the pointer pt
*pt = 5; //store 	the value 5 at the address
```

#### 数组名

在多数情况下，C++将数组名视为数组的第一个元素的地址。

```
int tacos[10]; //now tacos is the same as &tacos[0]
```

一种例外情况是，将sizeof运算符用于数组名用时，此时将返回整个数组的长度（单位为字节）。

#### 指针算术

C++允许将指针和整数相加。加1的结果等于原来的地址值加上指向的对象占用的总字节数。还可以将一个指针减去另一个指针，获得两 个指针的差。后一种运算将得到一个整数，仅当两个指针指向同一个数组（也可以指向超出结尾的一个位置）时，这种运算才有意义；这将得到两个元素的间隔。 

```cpp
int tacos[10] = {1,2,3,4,5,6,7,8,9};
int * pt =tacos;
pt = pt+1;
int *pe = &tacos[9];
pe = pe -1;
int diff  = pe - pt;
```

#### 数组的动态联编和静态联编

使用数组声明来创建数组时，将采用静态联编，即数组的长度在编译时设置：

```
int tacos[10];
```

使用new[ ]运算符创建数组时，将采用动态联编（动态数组），即 将在运行时为数组分配空间，其长度也将在运行时设置。使用完这种数 组后，应使用delete [ ]释放其占用的内存：

```
int size ;
int *pz = new int[size];
delete [] pz;
```

#### 数组表示法和指针表示法

使用方括号数组表示法等同于对指针解除引用

```
tacos[0] means *tacos means the value at address tacos
tacos[3] means *(tacos+3) means the value at address + 3 address
```

数组名和指针变量都是如此，因此对于指针和数组名，既可以使用指针表示法，也可以使用数组表示法。

```cpp
int *p = new int[10];
*pt = 5;
pt[0]=6;
pt[9]=44;
int coast[10];
*(coast + 4) = 12;
```

### 指针和字符串 

### 使用**new**创建动态结构

在运行时创建数组优于在编译时创建数组，对于结构也是如此。需 要在程序运行时为结构分配所需的空间，这也可以使用new运算符来完 成。通过使用new，可以创建动态结构。同样，“动态”意味着内存是在运行时，而不是编译时分配的

将new用于结构由两步组成：创建结构和访问其成员。要创建结构，需要同时使用结构类型和new。例如，要创建一个未命名的 inflatable类型，并将其地址赋给一个指针，可以这样做：

```
inflatable * ps = new inflatable;
```

这将把足以存储inflatable结构的一块可用内存的地址赋给ps。这种句法和C++的内置类型完全相同。 比较棘手的一步是访问成员。创建动态结构时，不能将成员运算符 句点用于结构名，因为这种结构没有名称，只是知道它的地址。C++专 门为这种情况提供了一个运符：箭头成员运算符（−>）。该运算符由 连字符和大于号组成，可用于指向结构的指针，就像点运算符可用于结 构名一样。例如，如果ps指向一个inflatable结构，则ps−>price是被指向 的结构的price成员

```cpp
struct thing {
	int good;
	int bad;
}
things grubnose = {3,435};
things * pt = &grubnose;
grubnose.good;
grubnose.bad;
pt->good;
pt->bad;
```

如果结构标识符是结构名，则使用句点运算符；如果标识符是指向结构的指针，则使用箭头运算符。

另一种访问结构成员的方法是，如果ps是指向结构的指针，则*ps就是被指向的值—结构本身。由于*ps是一个结构，因此（*ps）.price是 该结构的price成员。C++的运算符优先规则要求使用括号。 

```js

#include <iostream>
struct inflatable   // structure definition
{
    char name[20];
    float volume;
    double price;
};
int main()
{
    using namespace std;
    inflatable * ps = new inflatable; // allot memory for structure
    cout << "Enter name of inflatable item: ";
    cin.get(ps->name, 20);            // method 1 for member access
    cout << "Enter volume in cubic feet: ";
    cin >> (*ps).volume;              // method 2 for member access
    cout << "Enter price: $";
    cin >> ps->price;
    cout << "Name: " << (*ps).name << endl;              // method 2
    cout << "Volume: " << ps->volume << " cubic feet\n"; // method 1
    cout << "Price: $" << ps->price << endl;             // method 1
    delete ps;                        // free memory used by structure
    // cin.get();
    // cin.get();
    return 0;
}
//Enter name of inflatable item: Fabulous Frodo
//Enter volume in cubic feet: 1.4
//Enter price: $27.99
//Name: Fabulous Frodo
//Volume: 1.4 cubic feet
//Price: $27.99
```

一个使用new和delete的示例

下面介绍一个使用new和delete来存储通过键盘输入的字符串的示例。程序清单4.22定义了一个函数getname( )，该函数返回一个指向输入字符串的指针。该函数将输入读入到一个大型的临时数组中，然后使用new [ ]创建一个刚好能够存储该输入字符串的内存块，并返回一个指向该内存块的指针。对于读取大量字符串的程序，这种方法可以节省大量内存（实际编写程序时，使用string类将更容易，因为这样可以使用内置的new和delete）。 

假设程序要读取1000个字符串，其中最大的字符串包含79个字符，而大多数字符串都短得多。如果用char数组来存储这些字符串，则需要 1000个数组，其中每个数组的长度为80个字符。这总共需要80000个字节，而其中的很多内存没有被使用。另一种方法是，创建一个数组，它包含1000个指向char的指针，然后使用new根据每个字符串的需要分配相应数量的内存。这将节省几万个字节。是根据输入来分配内存，而不是为每个字符串使用一个大型数组。另外，还可以使用new根据需要的指针数量来分配空间。就目前而言，这有点不切实际，即使是使用1000个指针的数组也是这样，不过程序清单4.22还是演示了一些技巧。另外，为演示delete是如何工作的，该程序还用它来释放内存以便能够重新使用。

```cpp
// delete.cpp -- using the delete operator
#include <iostream>
#include <cstring>      // or string.h
using namespace std;
char * getname(void);   // function prototype
int main()
{
    char * name;        // create pointer but no storage

    name = getname();   // assign address of string to name
    cout << name << " at " << (int *) name << "\n";
    delete [] name;     // memory freed

    name = getname();   // reuse freed memory
    cout << name << " at " << (int *) name << "\n";
    delete [] name;     // memory freed again
    // cin.get();
    // cin.get();
    return 0;
}

char * getname()        // return pointer to new string
{
    char temp[80];      // temporary storage
    cout << "Enter last name: ";
    cin >> temp;
    char * pn = new char[strlen(temp) + 1];
    strcpy(pn, temp);   // copy string into smaller space

    return pn;          // temp lost when function ends
}
//Enter last name: Fredelddumpkin
//Fredelddumpkin at 0x600000f70000
//Enter last name: Pook
//Pook at 0x600000f70000
```

来看一下程序清单4.22中的函数getname( )。它使用cin将输入的单词放到temp数组中，然后使用new分配新内存，以存储该单词。程序需要strle（temp）+ 1个字符（包括空字符）来存储该字符串，因此将这个值提供给new。获得空间后，getname( )使用标准库函数strcpy( )将temp中的字符串复制到新的内存块中。该函数并不检查内存块是否能够容纳字符串，但getname( )通过使用new请求合适的字节数来完成了这样的工作。最后，函数返回pn，这是字符串副本的地址。 

在main( )中，返回值（地址）被赋给指针name。该指针是在main( )中定义的，但它指向getname( )函数中分配的内存块。然后，程序打印该字符串及其地址。接下来，在释放name指向的内存块后，main( )再次调用getname()。C++不保证新释放的内存就是下一次使用new时选择的内存，从程序运行结果可知，确实不是。 

在这个例子中，getname( )分配内存，而main( )释放内存。将new和delete放在不同的函数中通常并不是个好办法，因为这样很容易忘记使用delete。不过这个例子确实把new和delete分开放置了，只是为了说明这样做也是可以的。 

### 自动存储、静态存储和动态存储

根据用于分配内存的方法，C++有3种管理数据内存的方式：自动存储、静态存储和动态存储（有时也叫作自由存储空间或堆）。在存在 时间的长短方面，以这3种方式分配的数据对象各不相同.（C++11新增了第四种类型—线程存储

#### 自动存储 

在函数内部定义的常规变量使用自动存储空间，被称为自动变量 （automatic variable），这意味着它们在所属的函数被调用时自动产 生，在该函数结束时消亡。例如，程序清单4.22中的temp数组仅当getname( )函数活动时存在。当程序控制权回到main( )时，temp使用的内存将自动被释放。如果getname( )返回temp的地址，则main( )中的name指针指向的内存将很快得到重新使用。这就是在getname( )中使用new的原因之一。

实际上，自动变量是一个局部变量，其作用域为包含它的代码块。代码块是被包含在花括号中的一段代码。到目前为止，我们使用的所有 代码块都是整个函数。然而，在下一章将会看到，函数内也可以有代码块。如果在其中的某个代码块定义了一个变量，则该变量仅在程序执行该代码块中的代码时存在。 

自动变量通常存储在栈中。这意味着执行代码块时，其中的变量将依次加入到栈中，而在离开代码块时，将按相反的顺序释放这些变量， 这被称为后进先出（LIFO）。因此，在程序执行过程中，栈将不断地增大和缩小。 

#### 静态存储 

静态存储是整个程序执行期间都存在的存储方式。使变量成为静态的方式有两种：一种是在函数外面定义它；另一种是在声明变量时使用 关键字static：

```
static double fee = 56.50;
```

在K&R C中，只能初始化静态数组和静态结构，而C++ Release2.0（及后续版本）和ANSI C中，也可以初始化自动数组和自动结构。 然而，一些您可能已经发现，有些C++实现还不支持对自动数组和自动 结构的初始化。

自动存储和静态存储的关键在于：这些方法严格地限制了变量的寿命。变量可能存在于程序的整个生命周期（静态变量），也可能只是在特定函数被执行时存在（自动变量）。

#### 动态存储

new和delete运算符提供了一种比自动变量和静态变量更灵活的方法。它们管理了一个内存池，这在C++中被称为自由存储空间（free store）或堆（heap）。该内存池同用于静态变量和自动变量的内存是分开的.new和delete让您能够在一个函数中分配内存，而在另一个函数中释放它。因此，数据的生命周期不完全受程序或函数的生存时间控制。与使用常规变量相比，使用new和delete让程序员对程序如何使用内存有更大的控制权。然而，内存管理也更复杂了。在栈中，自动添加和删除机制使得占用的内存总是连续的，但new和delete 的相互影响可能导致占用的自由存储区不连续，这使得跟踪新分配内存的位置更困难

#### 栈、堆和内存泄漏

如果使用new运算符在自由存储空间（或堆）上创建变量后，没有调用delete，将发生什么情况呢？如果没有调用delete，则即使包含指针的内存由于作用域规则和对象生命周期的原因而被释放，在自由存储空间上动态分配的变量或结构也将继续存在。实际上，将会无法访 问自由存储空间中的结构，因为指向这些内存的指针无效。这将导致内存泄漏。被泄漏的内存将在程序的整个生命周期内都不可使用；这些内存被分配出去，但无法收回。极端情况（不过不常见）是，内存泄漏可能会非常严重，以致于应用程序可用的内存被耗尽，出现内 存耗尽错误，导致程序崩溃。另外，这种泄漏还会给一些操作系统或在相同的内存空间中运行的应用程序带来负面影响，导致它们崩溃,即使是最好的程序员和软件公司，也可能导致内存泄漏。要避免内存泄漏，最好是养成这样一种习惯，即同时使用new和delete运算符，在自由存储空间上动态分配内存，随后便释放它。C++智能指针有助于自动完成这种任务

## 类型组合

```cpp
struct antarctica_years_end {
	int year;
}
```

创建结构变量

```cpp
antarctica_years_end s01,s02,s03;
```

成员运算符访问成员

```
s01.year=1998;
```

可创建指向这种结构的指针

```
antarctica_years_end * pa =&s02;
```

将该指针设置为有效地址后，就可使用间接成员运算符来访问成员

```
pa -> year =1999;
```

可创建结构数组:

```
antarctica_years_end trio[3];
```

可以使用成员运算符访问元素的成员

```
trio[0].year = 2003;
(trio+1)->year = 2004;//same as trio[1].year =2004
```

创建指针数组

```
const antarctica_years_end *arp[3] = {&s01,&s02,&s03};
```

可创建指向上述数组的指针

```
const antarctica_years_end	**ppa =arp //arp指向数组的第一个值的指针，然后他的值又是指针，所以要获取真正的值需要** 
```

C++11版本的auto提供的方便。编译器知道arp的类型，能够正确地推断出ppb的类型：

```
auto ppb = arp；
```

如何使用ppa来访问数据呢？由于ppa是一个指向结构指针的指针， 因此*ppa是一个结构指针，可将间接成员运算符应用于它： 

```
std ：：cout << (*ppa)->year << std::endl;
std ：：cout << (*ppa+1)->year << std::endl;
```

由于ppa指向arp的第一个元素，因此*ppa为第一个元素，即&s01。所以，(*ppa)->year为s01的year成员。在第二条语句中，ppb+1指向下一 个元素arp[1]，即&s02。其中的括号必不可少，这样才能正确地结合。

```cpp
// mixtypes.cpp --some type combinations
#include <iostream>

struct antarctica_years_end
{
    int year;
    /* some really interesting data, etc. */
};

int main()
{
    antarctica_years_end s01, s02, s03;
    s01.year = 1998;
    antarctica_years_end * pa = &s02;
    pa->year = 1999;
    antarctica_years_end trio[3]; // array of 3 structures
    trio[0].year = 2003;
    std::cout << trio->year << std::endl;
    const antarctica_years_end * arp[3] = {&s01, &s02, &s03};
    std::cout << arp[1]->year << std::endl;
    const antarctica_years_end ** ppa = arp;
    auto ppb = arp; // C++0x automatic type deduction
// or else use const antarctica_years_end ** ppb = arp;
    std::cout << (*ppa)->year << std::endl;
    std::cout << (*(ppb+1))->year << std::endl;
    // std::cin.get();
    return 0;
}
//2003
//1999
//1998
//1999
```

## 数组的替代品

模板类vector和array是数组的替代品

### 模板类vector

模板类vector类似于string类，也是一种动态数组。您可以在运行阶段设置vector对象的长度，可在末尾附加新数据，还可在中间插入新数据。基本上，它是使用new创建动态数组的替代品。实际上，vector类确实使用new和delete来管理内存，但这种工作是自动完成的

这里不深入探讨模板类意味着什么，而只介绍一些基本的实用知识。首先，要使用vector对象，必须包含头文件vector。其次，vector包 含在名称空间std中，因此您可使用using编译指令、using声明或 std::vector。第三，模板使用不同的语法来指出它存储的数据类型。第 四，vector类使用不同的语法来指定元素数

```
vector<typename> vt(n_elem);
```

其中参数n_elem可以是整型常量，也可以是整型变量。

### 模板类array（C++11）

vector类的功能比数组强大，但付出的代价是效率稍低。如果您需要的是长度固定的数组，使用数组是更佳的选择，但代价是不那么方便 和安全。有鉴于此，C++11新增了模板类array，它也位于名称空间std中。与数组一样，array对象的长度也是固定的，也使用栈（静态内存分配），而不是自由存储区，因此其效率与数组相同，但更方便，更安全。要创建array对象，需要包含头文件array。array对象的创建语法与 vector稍有不同： 

```cpp
#include <array>
...
using namespace std;
array<int 5> ai;
array<double,4> ad = {1.2,2.1,3.43,4.3}
```

推而广之，下面的声明创建一个名为arr的array对象，它包含n_elem个类型为typename的元素：

```
array<typename,n_elem> arr;
```

与创建vector对象不同的是，n_elem不能是变量。在C++11中，可将列表初始化用于vector和array对象，但在C++98 中，不能对vector对象这样做。 

### 比较数组、vector对象和array对象

```cpp
// choices.cpp -- array variations
#include <iostream>
#include <vector>   // STL C++98
#include <array>    // C++0x
int main()
{
    using namespace std;
// C, original C++
    double a1[4] = {1.2, 2.4, 3.6, 4.8};
// C++98 STL
    vector<double> a2(4);   // create vector with 4 elements
// no simple way to initialize in C98
    a2[0] = 1.0/3.0;
    a2[1] = 1.0/5.0;
    a2[2] = 1.0/7.0;
    a2[3] = 1.0/9.0;
// C++0x -- create and initialize array object
    array<double, 4> a3 = {3.14, 2.72, 1.62, 1.41};
    array<double, 4> a4;
    a4 = a3;     // valid for array objects of same size
// use array notation
    cout << "a1[2]: " << a1[2] << " at " << &a1[2] << endl;
    cout << "a2[2]: " << a2[2] << " at " << &a2[2] << endl;
    cout << "a3[2]: " << a3[2] << " at " << &a3[2] << endl;
    cout << "a4[2]: " << a4[2] << " at " << &a4[2] << endl;
// misdeed
    a1[-2] = 20.2;
    cout << "a1[-2]: " << a1[-2] <<" at " << &a1[-2] << endl;
    cout << "a3[2]: " << a3[2] << " at " << &a3[2] << endl;
    cout << "a4[2]: " << a4[2] << " at " << &a4[2] << endl;
    //  cin.get();
    return 0;
}
//a1[2]: 3.6 at 0x16ae27710
//a2[2]: 0.142857 at 0x600001b35110
//a3[2]: 1.62 at 0x16ae276d0
//a4[2]: 1.62 at 0x16ae276b0
//a1[-2]: 20.2 at 0x16ae276f0
//a3[2]: 1.62 at 0x16ae276d0
//a4[2]: 1.62 at 0x16ae276b0
```

首先，注意到无论是数组、vector对象还是array对象，都可使用标准数组表示法来访问各个元素。其次，从地址可知，array对象和数组存储在相同的内存区域（即栈）中，而vector对象存储在另一个区域（自由存储区或堆）中。第三，注意到可以将一个array对象赋给另一个array对象；而对于数组，必须逐元素复制数据。

```
a1[-2] = 20.2;//=>*(a1-2)
```

其含义如下：找到a1指向的地方，向前移两个double元素，并将20.2存储到目的地。也就是说，将信息存储到数组的外面。与C语言一 样，C++也不检查这种超界错误

也可以这样做

```
a2.at(1) = 2.3;
```

中括号表示法和成员函数at()的差别在于，使用at()时，将在运行期间捕获非法索引，而程序默认将中断。这种额外检查的代价是运行时间 更长，这就是C++让允许您使用任何一种表示法的原因所在。另外，这些类还让您能够降低意外超界错误的概率。例如，它们包含成员函数begin()和end()，让您能够确定边界，以免无意间超界

## 总结

数组、结构和指针是C++的3种复合类型。数组可以在一个数据对象中存储多个同种类型的值。通过使用索引或下标，可以访问数组中各 个元素。

结构可以将多个不同类型的值存储在同一个数据对象中，可以使用成员关系运算符（.）来访问其中的成员。使用结构的第一步是创建结 构模板，它定义结构存储了哪些成员。模板的名称将成为新类型的标识符，然后就可以声明这种类型的结构变量

共用体可以存储一个值，但是这个值可以是不同的类型，成员名指出了使用的模式。

指针是被设计用来存储地址的变量。我们说，指针指向它存储的地址。指针声明指出了指针指向的对象的类型。对指针应用解除引用运算 符，将得到指针指向的位置中的值。

字符串是以空字符为结尾的一系列字符。字符串可用引号括起的字符串常量表示，其中隐式包含了结尾的空字符。可以将字符串存储在 char数组中，可以用被初始化为指向字符串的char指针表示字符串。函数strlen( )返回字符串的长度，其中不包括空字符。函数strcpy( )将字符串从一个位置复制到另一个位置。在使用这些函数时，应当包含头文件cstring或string.h。

头文件string支持的C++ string类提供了另一种对用户更友好的字符串处理方法。具体地说，string对象将根据要存储的字符串自动调整其 大小，用户可以使用赋值运算符来复制字符串。

new运算符允许在程序运行时为数据对象请求内存。该运算符返回获得内存的地址，可以将这个地址赋给一个指针，程序将只能使用该指 针来访问这块内存。如果数据对象是简单变量，则可以使用解除引用运算符（*）来获得其值；如果数据对象是数组，则可以像使用数组名那样使用指针来访问元素；如果数据对象是结构，则可以用指针解除引用运算符（->）来访问其成员。

指针和数组紧密相关。如果ar是数组名，则表达式ar[i]被解释为 *（ar + i），其中数组名被解释为数组第一个元素的地址。这样，数组名的作用和指针相同。反过来，可以使用数组表示法，通过指针名来访 问new分配的数组中的元素。

运算符new和delete允许显式控制何时给数据对象分配内存，何时将内存归还给内存池。自动变量是在函数中声明的变量，而静态变量在 函数外部或者使用关键字static声明的变量，这两种变量都不太灵活。自动变量在程序执行到其所属的代码块（通常是函数定义）时产生，在离开该代码块时终止。静态变量在整个程序周期内都存在。

C++98新增的标准模板库（STL）提供了模板类vector，它是动态数组的替代品。C++11提供了模板类array，它是定长数组的替代品。

# 循环和关系表达式

## for循环

```cpp
// forloop.cpp -- introducing the for loop
#include <iostream>
int main()
{
    using namespace std;
    int i;  // create a counter
//   initialize; test ; update
    for (i = 0; i < 5; i++)
        cout << "C++ knows loops.\n";
    cout << "C++ knows when to stop.\n";
    // cin.get();
    return 0;
}
//C++ knows loops.
//C++ knows loops.
//C++ knows loops.
//C++ knows loops.
//C++ knows loops.
//C++ knows when to stop.
```

### for循环的组成部分 

for循环的组成部分完成下面这些步骤。

1．设置初始值。 

2．执行测试，看看循环是否应当继续进行。 

3．执行循环操作。 

4．更新用于测试的值。

C++循环设计中包括了这些要素，很容易识别。初始化、测试和更新操作构成了控制部分，这些操作由括号括起。其中每部分都是一个表 达式，彼此由分号隔开。控制部分后面的语句叫作循环体，只要测试表 达式为true，它便被执行：

```
for(initialization;test-expression;update-expression)
	body
```

C++语法将整个for看作一条语句—虽然循环体可以包含一条或多条语句。（包含多条语句时，需要使用复合语句或代码块，这将在本章后 面进行讨论。）

循环只执行一次初始化。通常，程序使用该表达式将变量设置为起始值，然后用该变量计算循环周期。

test-expression（测试表达式）决定循环体是否被执行。通常，这个表达式是关系表达式，即对两个值进行比较。这个例子将i的值同5进行比较，看i是否小于5。如果比较结果为真，则程序将执行循环体。实际上，C++并没有将test-expression的值限制为只能为真或假。可以使用任意表达式，C++将把结果强制转换为bool类型。因此，值为0的表达式将被转换为bool值false，导致循环结束。如果表达式的值为非零，则被强制转换为bool值true，循环将继续进行

```cpp
// num_test.cpp -- use numeric test in for loop
#include <iostream>
int main()
{
    using namespace std;
    cout << "Enter the starting countdown value: ";
    int limit;
    cin >> limit;
    int i;
    for (i = limit; i; i--)     // quits when i is 0
        cout << "i = " << i << "\n";
    cout << "Done now that i = " << i << "\n";
    // cin.get();
    // cin.get();
    return 0;
}
//Enter the starting countdown value: 4
//i = 4
//i = 3
//i = 2
//i = 1
//Done now that i = 0
```

注意，循环在i变为0后结束

关系表达式（如i<5）是如何得到循环终止值0的呢？在引入bool类型之前，如果关系表达式为true，则被判定为1；如果为false，则被判定为0。因此，表达式3<5的值为1，而5<5的值为0。然而，C++添加了bool类型后，关系表达式就判定为bool字面值true和false，而不是1和0了。这种变化不会导致不兼容的问题，因为C++程序在需要整数值的地方将把true和false分别转换为1和0，而在需要bool值的地方将把0转换为false，非0转换为true。 

for循环是入口条件（entry-condition）循环。这意味着在每轮循环之前，都将计算测试表达式的值，当测试表达式为false时，将不会执行循环体

```cpp
// express.cpp -- values of expressions
#include <iostream>
int main()
{
    using namespace std;
    int x;

    cout << "The expression x = 100 has the value ";
    cout << (x = 100) << endl;
    cout << "Now x = " << x << endl;
    cout << "The expression x < 3 has the value ";
    cout << (x < 3) << endl;
    cout << "The expression x > 3 has the value ";
    cout << (x > 3) << endl;
    cout.setf(ios_base::boolalpha);   //a newer C++ feature
    cout << "The expression x < 3 has the value ";
    cout << (x < 3) << endl;
    cout << "The expression x > 3 has the value ";
    cout << (x > 3) << endl;
    /// cin.get();
    return 0;
}
//the expression x = 100 has the value 100
//Now x = 100
//The expression x < 3 has the value 0
//The expression x > 3 has the value 1
//The expression x < 3 has the value false
//The expression x > 3 has the value true
```

通常，cout在显示bool值之前将它们转换为int，但cout.setf（ios：：boolalpha）函数调用设置了一个标记，该标记命令cout显示true和false，而不是1和0。

```
age = 100
```

从表达式到语句的转变很容易，只要加分号即可;

```
age = 100;
```

for结构的控制部分由3个表达式组成，它们由分号分隔。然而，C++循环允许像下面这样做：

```
for(int i = 0; i<5 ; i++)
```

也就是说，可以在for循环的初始化部分中声明变量。这很方便，但并不适用于原来的句法，因为声明不是表达式。这种一度是非法的行为最初是通过定义一种新的表达式—声明语句表达式（declaration- statement expression）—来合法化的，声明语句表达式不带分号声明，只能出现在for语句中。然而，这种调整已经被取消了，代之以将for语句的句法修改成下面这样：

```
for (for-init-statement condition;expression)
	statement
```

因为这里只有一个分号（而不是两个分号）。但是这是允许的，因为for-init-statement被视为一条语句，而语句有自己的分号。对于for-init-statement来说，它既可以是表达式语句，也可以是声明。这种句法规则用语句替换了后面跟分号的表达式，语句本身有自己的分号

在for-init-statement中声明变量还有其实用的一面，这也是应该知道的。这种变量只存在于for语句中，也就是说，当程序离开循环后，这种变量将消失： 

```
for(int i = 0;i<5;i++)
	cout<<"c++ knows loop \n";
```

### 回到for循环 

```cpp
// formore.cpp -- more looping with for
#include <iostream>
const int ArSize = 16;      // example of external declaration
int main()
{
    long long factorials[ArSize];
    factorials[1] = factorials[0] = 1LL;
    for (int i = 2; i < ArSize; i++)
        factorials[i] = i * factorials[i-1];
    for (int i = 0; i < ArSize; i++)
        std::cout << i << "! = " << factorials[i] << std::endl;
    // std::cin.get();
    return 0;
}
//0! = 1
//1! = 1
//2! = 2
//3! = 6
//4! = 24
//5! = 120
//6! = 720
//7! = 5040
//8! = 40320
//9! = 362880
//10! = 3628800
//11! = 39916800
//12! = 479001600
//13! = 6227020800
//14! = 87178291200
//15! = 1307674368000
```

### 修改步长

```cpp
// bigstep.cpp -- count as directed
#include <iostream>
int main()
{
    using std::cout;    // a using declaration
    using std::cin;
    using std::endl;;
    cout << "Enter an integer: ";
    int by;
    cin >> by;
    cout << "Counting by " << by << "s:\n";
    for (int i = 0; i < 100; i = i + by)
        cout << i << endl;
    // cin.get();
    // cin.get();
    return 0;
}
//Enter an integer: 17
//Counting by 17s:
//0
//17
//34
//51
//68
//85
```

### 使用for循环访问字符串

```cpp
// forstr1.cpp -- using for with a string
#include <iostream>
#include <string>
int main()
{
    using namespace std;
    cout << "Enter a word: ";
    string word;
    cin >> word;

    // display letters in reverse order
    for (int i = word.size() - 1; i >= 0; i--)//size可以获得字符串的个数
        cout << word[i];
    cout << "\nBye.\n";
    // cin.get();
    // cin.get();
    return 0;
}
//Enter a word: animal
//lamina
//Bye.
```

如果所用的实现没有添加新的头文件，则必须使用string.h，而不是cstring。

### 副作用和顺序点 

副作用（side effect）指的是在计算表达式时对某些东西（如存储在变量中的值）进行了修改；顺序点 （sequence point）是程序执行过程中的一个点，在这里，进入下一步之 前将确保对所有的副作用都进行了评估。在C++中，语句中的分号就是一个顺序点，这意味着程序处理下一条语句之前，赋值运算符、递增运 =算符和递减运算符执行的所有修改都必须完成。另外，任何完整的表达式末尾都是一个顺序点

何为完整表达式呢？它是这样一个表达式：不是另一个更大表达式的子表达式。完整表达式的例子有：表达式语句中的表达式部分以及用 作while循环中检测条件的表达式。

顺序点有助于阐明后缀递增何时进行

```cpp
while(gust++<10)
        cout<<gust<<endl;
```



```
y=(4+x++)+(6+x++)
```

表达式4 + x++不是一个完整表达式，因此，C++不保证x的值在计算子表达式4 + x++后立刻增加1。在这个例子中，整条赋值语句是一个 完整表达式，而分号标示了顺序点，因此C++只保证程序执行到下一条语句之前，x的值将被递增两次。C++没有规定是在计算每个子表达式之后将x的值递增，还是在整个表达式计算完毕后才将x的值递增，有鉴于此，您应避免使用这样的表达式。

### 前缀格式和后缀格式 

```
for(n=1im;n>0;n--)

for(n-lim;n>0;--n)

在这里，这两种没有区别
但是在这种情况下，用户这样定义前缀函数：将值加1，然后返回结果；但 后缀版本首先复制一个副本，将其加1，然后将复制的副本返回。因 此，对于类而言，前缀版本的效率比后缀版本高
```

总之，对于内置类型，采用哪种格式不会有差别；但对于用户定义的类型，如果有用户定义的递增和递减运算符，则前缀格式的效率更 高

### 递增**/**递减运算符和指针

可以将递增运算符用于指针和基本变量。本书前面介绍过，将递增运算符用于指针时，将把指针的值增加其指向的数据类型占用的字节 数，这种规则适用于对指针递增和递减：

```
double arr[5] = {21.1,32.8,23.4,45.2,37.4};
double *pt = arr;
++pt;
```

也可以结合使用这些运算符和*运算符来修改指针指向的值。将*和++同时用于指针时提出了这样的问题：将什么解除引用，将什么递增。 这取决于运算符的位置和优先级。前缀递增、前缀递减和解除引用运算符的优先级相同，以从右到左的方式进行结合。后缀递增和后缀递减的优先级相同，但比前缀运算符的优先级高，这两个运算符以从左到右的方式进行结合

前缀运算符的从右到到结合规则意味着\*++pt的含义如下：现将++应用于pt（因为++位于\*的右边），然后将*应用于被递增后的pt：

```
double x = *++pt;
```

另一方面，++*pt意味着先取得pt指向的值，然后将这个值加1：

```
++*pt;
```

在这种情况下，pt仍然指向arr[2]。 

```
(*pt)++;
```

圆括号指出，首先对指针解除引用，得到24.4。然后，运算符++将这个值递增到25.4，pt仍然指向arr[2]。

```
*pt++;
```

后缀运算符++的优先级更高，这意味着将运算符用于pt，而不是 *pt，因此对指针递增。然而后缀运算符意味着将对原来的地址 （&arr[2]）而不是递增后的新地址解除引用，因此*pt++的值为arr[2]， 即25.4，但该语句执行完毕后，pt的值将为arr[3]的地址。

### 组合赋值运算符

| 操作符 | 作用L为左操作符，R为右操作符 |
| ------ | ---------------------------- |
| +=     | L=L+R                        |
| -=     | L=L-R                        |
| *=     | L=L*R                        |
| /=     | L=L/R                        |
| %=     | L=L%R                        |

### 复合语句（语句块）

在for循环体中有多条语句时，需要使用花括号

```cpp
// block.cpp -- use a block statement
#include <iostream>
int main()
{
    using namespace std;
    cout << "The Amazing Accounto will sum and average ";
    cout << "five numbers for you.\n";
    cout << "Please enter five values:\n";
    double number;
    double sum = 0.0;
    for (int i = 1; i <= 5; i++)
    {                                   // block starts here
        cout << "Value " << i << ": ";//如果没有花括号，只会执行第一条作为循环语句
        cin >> number;
        sum += number;
    }                                   // block ends here
    cout << "Five exquisite choices indeed! ";
    cout << "They sum to " << sum << endl;
    cout << "and average to " << sum / 5 << ".\n";
    cout << "The Amazing Accounto bids you adieu!\n";
    // cin.get();
    // cin.get();
    return 0;
}
//The Amazing Accounto will sum and average five numbers for you.
//Please enter five values:
//Value 1: 1942
//Value 2: 1948
//Value 3: 1957
//Value 4: 1974
//Value 5: 1980
//Five exquisite choices indeed! They sum to 9801
//and average to 1960.2.
//The Amazing Accounto bids you adieu!
```

复合语句还有一种有趣的特性。如果在语句块中定义一个新的变量，则仅当程序执行该语句块中的语句时，该变量才存在。执行完该语 句块后，变量将被释放。这表明此变量仅在该语句块中才是可用的：

注意，在外部语句块中定义的变量在内部语句块中也是被定义了的。

如果在一个语句块中声明一个变量，而外部语句块中也有一个这种名称的变量，在块中内部变量会覆盖外部变量

### 其他语法技巧**—**逗号运算符 
