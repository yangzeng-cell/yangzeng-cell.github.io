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
