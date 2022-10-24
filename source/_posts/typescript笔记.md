---
title: typescript笔记
date: 2022-08-05 10:57:32
tags:
- [typescript]
categories:
- [typescript, note]
---
#### 1.在定义一个变量时，基本数据类型可以用类型推断，不用定义类型

```
let msg = "hello there!";
```

#### 2.ts的常用的数据类型

```
string,number,boolean,arrays,any,function,object,union type,Type Aliases,Interfaces，Type Assertions，bigint，symbol,Type Assertions,.Literal Types,Enums ,null,undefined,any,unknown,void,never ,Tuple
```

#### 3.interface和type aliases的区别

```
实现继承的方式不同
interface Animal {
  name: string
}

interface Bear extends Animal {
  honey: boolean
}
type Animal = {
  name: string
}

type Bear = Animal & { 
  honey: boolean 
}

添加新的字段
interface Window {
  title: string
}

interface Window {
  ts: TypeScriptAPI
}
类型别名不能够修改已经创建的字段
type Window = {
  title: string
}

type Window = {
  ts: TypeScriptAPI
}

 // Error: Duplicate identifier 'Window'.
const src = 'const a = "Hello World"';
window.ts.transpileModule(src, {});
接口只能声明对象，不能够重命名原语
类型别名不能够声明合并，但是接口可以

如果是定义非对象类型，通常推荐使用type
如果是定义对象类型，那么他们是有区别的：
interface 可以重复的对某个接口来定义属性和方法；
而type定义的是别名，别名是不能重复的
```

#### 4.Type Assertions类型断言

```
TypeScript 只允许类型断言转换为更具体或更不具体的类型版本，而不可以转成没有关系的类型

const a = (expr as any) as T;  可以将两种不相关的类型进行转换时，要进行两次断言，先断雁城any或者unknown
```

#### 5.Literal Types 文本类型

```
const constantString = "Hello World";//使用const定义的常量类型

function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}

您可以使用 as const 将整个对象转换为类型文字：
const req = { url: "https://example.com", method: "GET" } as const;
handleRequest(req.url, req.method);
```

#### 6.Enums 枚举

```tsx
// 定义枚举类型
enum Direction {
  LEFT,
  RIGHT
}

const d1: Direction = Direction.LEFT

function turnDirection(direction: Direction) {
  switch(direction) {
    case Direction.LEFT:
      console.log("角色向左移动一个格子")
      break
    case Direction.RIGHT:
      console.log("角色向右移动一个格子")
      break
  }
}

// 监听键盘的点击
turnDirection(Direction.LEFT)

export {}


```



```tsx
enum Direction {
  LEFT = "LEFT",
  RIGHT = "RIGHT"
}

enum Operation {
  Read = 1 << 0,//位运算符
  Write = 1 << 1,
  foo = 1 << 2
}

const d1: Direction = Direction.LEFT
```



#### 7.ts的运行环境

```
方式一：tsc math.ts   node math.js  这种方式会比较麻烦
方式二：webpack中配置本地的运行环境  使用ts-loader
方式三：使用ts-node
npm install ts-node -g
npm install tslib @types/node -g
ts-node math.ts
```

#### 8.unkonwn类型和any类型的区别

任何类型都可以是any类型,ts不会对any类型的变量进行类型检测，任何类型也是可以赋值给unknown,但是使用这个变量必须进行类型检查，否则就会报错

![QQ截图20220715095600.png](https://img1.imgtp.com/2022/08/05/Lt1elC2b.png)

#### 9.Tuple类型(元组)

元组类型就是不同类型的数组的集合

![QQ截图20220715101013.png](https://img1.imgtp.com/2022/08/05/PyfvKidj.png)

#### 10.Never类型

##### Never类型指永不存在的类型。 例如，never 类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型

#### 11.函数参数

![QQ截图20220715111132.png](https://img1.imgtp.com/2022/08/05/8e9QZ1fu.png)

#### 12.匿名函数的参数类型

![QQ截图20220715111344.png](https://img1.imgtp.com/2022/08/05/UiMI6HEs.png)

#### 13.函数参数的对象类型

![QQ截图20220715112229.png](https://img1.imgtp.com/2022/08/05/Itu8eHI1.png)

#### 14.函数参数对象可选参数

![QQ截图20220715112745.png](https://img1.imgtp.com/2022/08/05/4knXfTJ1.png)

#### 15.联合类型

![QQ截图20220715114922.png](https://img1.imgtp.com/2022/08/05/xI0aQEuD.png)

#### 16.一个参数一个可选类型的时候, 它其实类似于是这个参数是 类型|undefined 的联合类型

#### 17.非空类型断言!

![QQ截图20220715142334.png](https://img1.imgtp.com/2022/08/05/T6LzBxdD.png)

#### 18.可选链操作

![QQ截图20220715151237.png](https://img1.imgtp.com/2022/08/05/TaGoHMzn.png)

#### 19.!!运算符

可以将其他数据类型转换成boolean

#### 20.??操作符  空值合并操作符

```
const messgae1 = "1111";
const content = messgae1 ?? "你好";
如果message1是null或者undefined,content就会使用后面的值，message是空字符串不生效
```

#### 21.类型缩小  Type Narrowing

类型缩小就是将例如联合类型进行精确到具体的类型

常见的类型保护有

typeof

===，！==

instanceof

in

.....

![QQ截图20220716003305.png](https://img1.imgtp.com/2022/08/05/xmoxhxl5.png)

![QQ截图20220716003335.png](https://img1.imgtp.com/2022/08/05/DnVQP9fW.png)

![QQ截图20220716003417](https://img1.imgtp.com/2022/08/05/ECM7n6A2.png)

![QQ截图20220716003433](https://img1.imgtp.com/2022/08/05/eDTnB12m.png)

![QQ截图20220716003446](https://img1.imgtp.com/2022/08/05/s38SLBN0.png)

#### 22.函数类型定义

![](https://img1.imgtp.com/2022/08/05/E4FrxA0i.png)

#### 23.参数的可选类型

![](https://img1.imgtp.com/2022/08/05/5GU46enS.png)

#### 24.参数的默认值

![](https://img1.imgtp.com/2022/08/05/0pGV9IBH.png)

#### 25.剩余参数

![](https://img1.imgtp.com/2022/08/05/b2Gi65SO.png)

#### 26.指定this的绑定

![](https://img1.imgtp.com/2022/08/05/PLXzyda7.png)

#### 27.函数的重载

通过联合类型的有两个缺点：

 1.需要进行很多类型逻辑判断

 2.返回值类型不能判定

![](https://img1.imgtp.com/2022/08/05/Xl0sI2DM.png)

![](https://img1.imgtp.com/2022/08/05/obc6YOcv.png)

#### 27.类的定义

![](https://img1.imgtp.com/2022/08/05/GQd3h5pF.png)

#### 28.类的继承

![](https://img1.imgtp.com/2022/08/05/NwBuKZH2.png)

#### 29.类的多态

![](https://img1.imgtp.com/2022/08/05/95zR2UQ2.png)

#### 30.类的成员修饰符

private 修饰的属性和方法只能在该类内部使用

protected修饰的属性和方法只能在类本身及其子类内部使用

public是默认的成员修饰符，可以在外部，类，子类使用

![](https://img1.imgtp.com/2022/08/05/Pw02xjP0.png)

#### 31.readonly修饰符

![](https://img1.imgtp.com/2022/08/05/KrYErakm.png)

#### 32.访问器setter/getter

![](https://img1.imgtp.com/2022/08/05/eVx6dPgv.png)

#### 33.类的静态成员

![](https://img1.imgtp.com/2022/08/05/ESG5cMLo.png)

#### 34.抽象类abstract

什么是 抽象方法? 在TypeScript中没有具体实现的方法(没有方法体)，就是抽象方法

抽象方法，必须存在于抽象类中

抽象类是使用abstract声明的类

![](https://img1.imgtp.com/2022/08/05/XmcOILgK.png)

#### 35.类本身也可以作为另外一变量的类型

#### 36.接口的声明

 ![](https://img1.imgtp.com/2022/08/05/3ANVJqpG.png)

#### 37.接口的索引类型

![](https://img1.imgtp.com/2022/08/05/1pM1SZre.png)

#### 38.函数类型

interface也可以定义函数，建议使用类型别名列定义函数

![](https://img1.imgtp.com/2022/08/05/xVRVoVov.png)

#### 39.接口的继承

![](https://img1.imgtp.com/2022/08/05/VlVrCRbV.png)

![](https://img1.imgtp.com/2022/08/05/gvacaNKp.png)

#### 40.交叉类型

![](https://img1.imgtp.com/2022/08/05/kExwu85c.png)

#### 41.字面量赋值

#### ![](https://img1.imgtp.com/2022/08/05/LVx7kzYn.png)

![QQ截图20220716171033.png](https://img1.imgtp.com/2022/08/05/UxFV3w0L.png)

#### 42.枚举类型

枚举其实就是将一组可能出现的值，一个个列举出来，定义在一个类型中，这个类型就是枚举类型； 

枚举允许开发者定义一组命名常量，常量可以是数字、字符串类型

![QQ截图20220716172406.png](https://img1.imgtp.com/2022/08/05/JUcGQCK2.png)

![QQ截图20220716172417.png](https://img1.imgtp.com/2022/08/05/i4HcVm8N.png)

#### 43.泛型

![QQ截图20220717020140.png](https://img1.imgtp.com/2022/08/05/A6dtGWoy.png)

![QQ截图20220717020140.png](https://img1.imgtp.com/2022/08/05/A6dtGWoy.png)

```js
function Foo<T,E>(a:T,b:E){}
```

还可以传入多个类型

常见范型的缩写

T：是type的缩写。代表类型

K,V:是key和value的缩写

E：是element的缩写

O：是Object的缩写

#### 44.泛型接口的定义

![QQ截图20220717020609.png](https://img1.imgtp.com/2022/08/05/2ZbKkBg2.png)

#### 45.泛型作为类

![QQ截图20220717092242.png](https://img1.imgtp.com/2022/08/05/c0ucka5e.png)

#### 46.泛型约束

![QQ截图20220717092443.png](https://img1.imgtp.com/2022/08/05/A9jQ0E9R.png)

在范型参数使用约束

**举个栗子：我们希望获取一个对象给定属性名的值**

我们需要确保我们不会获取 obj 上不存在的属性；

所以我们在两个类型之间建立一个约束；

```js
// 传入的key类型, obj当中key的其中之一
interface IKun {
  name: string
  age: number
}

type IKunKeys = keyof IKun // "name"|"age"
//keyof 可以获取key的联合类型
function getObjectProperty<O, K extends keyof O>(obj: O, key: K){
  return obj[key]
}

const info = {
  name: "why",
  age: 18,
  height: 1.88
}
```

#### 47.**映射类型（Mapped Types）**

**有的时候，一个类型需要基于另外一个类型，但是你又不想拷贝一份，这个时候可以考虑使用映射类型**，而且可以改变属性的特性，例如可以改变属性的readonly，是否可选

大部分内置的工具都是通过映射类型来实现的；

大多数类型体操的题目也是通过映射类型完成的

**映射类型建立在索引签名的语法上：**

映射类型，就是使用了 PropertyKeys 联合类型的泛型；

其中 PropertyKeys 多是通过 keyof 创建，然后循环遍历键名创建一个类型

```tsx
// TypeScript提供了映射类型: 函数
// 映射类型不能使用interface定义
type MapPerson<Type> = {
  readonly [Property in keyof Type]?: Type[Property]//readonly可以将属性转成只读属性 ？可以转成可选属性
}

interface IPerson {
  name: string
  age: number
  height: number
  address: string
}

type IPersonOptional = MapPerson<IPerson>

const p: IPersonOptional = {

}
```

#### 48.**映射修饰符（Mapping Modifiers）**

**在使用映射类型时，有两个额外的修饰符可能会用到：**

一个是 readonly，用于设置属性只读；

 一个是 ? ，用于设置属性可选；

**你可以通过前缀 - 或者 + 删除或者添加这些修饰符，如果没有写前缀，相当于使用了 + 前缀**

```ts
type MapPerson<Type> = {
  -readonly [Property in keyof Type]-?: Type[Property]//- 表示删除readonly和？+表示添加
}

interface IPerson {
  name: string
  age?: number
  readonly height: number
  address?: string
}

// 
type IPersonRequired = MapPerson<IPerson>

const p: IPersonRequired = {
  name: "why",
  age: 18,
  height: 1.88,
  address: "广州市"
}
```

#### 49.**内置工具和类型体操**

**类型系统其实在很多语言里面都是有的，比如Java、Swift、C++等等，但是相对来说TypeScript的类型非常灵活：**

 这是因为TypeScript的目的是为JavaScript添加一套类型校验系统，因为JavaScript本身的灵活性，也让TypeScript类型系统

不得不增加更附加的功能以适配JavaScript的灵活性；

 所以TypeScript是一种可以支持类型编程的类型系统； 

◼ **这种类型编程系统为TypeScript增加了很大的灵活度，同时也增加了它的难度：**

 如果你不仅仅在开发业务的时候为自己的JavaScript代码增加上类型约束，那么基本不需要太多的类型编程能力；

 但是如果你在开发一些框架、库，或者通用性的工具，为了考虑各种适配的情况，就需要使用类型编程； 

◼ **TypeScript本身为我们提供了类型工具，帮助我们辅助进行类型转换（前面有用过关于this的类型工具）。**

◼ **很多开发者为了进一步增强自己的TypeScript编程能力，还会专门去做一些类型体操的题目：**

 https://github.com/type-challenges/type-challenges

 https://ghaiklor.github.io/type-challenges-solutions/en/

◼ **我们课堂上会学习TypeScript的编程能力的语法，并且通过学习内置工具来练习一些类型体操的题目**

#### 49.namespace

**命名空间不再建议使用，使用模块替代**

namespace会形成独立的空间，里面的内容需要使用export，外部才可以使用，否则外部无法访问

![QQ截图20220717174941.png](https://img1.imgtp.com/2022/08/05/qr2xvkmZ.png)
![QQ截图20220717175005.png](https://img1.imgtp.com/2022/08/05/Eq2OIARP.png)
48.命名空间的拆分

可以将命名空间的内容拆分到多个文件中

#### 50..d.ts文件  类型声明文件

它仅仅用来做类型检测，告知typescript我们有哪 些类型,他和普通的ts文件的区别是，他只能做类型盛名，不可以写业务代码，而且不需要使用export导出，他是全局的

那么typescript会在哪里查找我们的类型声明呢？

内置类型声明；

外部定义类型声明；

自己定义类型声明；

类型声明文件主要适用于外部类库，例如引入的npm包没有做类型声明，则需要自己写类型声明文件

#### 51.内置类型声明

内置类型声明是typescript自带的、帮助我们内置了JavaScript运行时的一些标准化API的声明文件。包括比如Math、Date等内置类型，也包括DOM API，比如Window、Document等； 内置类型声明通常在我们安装typescript的环境中会带有的；

https://github.com/microsoft/TypeScript/tree/main/lib

**TypeScript 使用模式命名这些声明文件lib.[something].d.ts**

**内置声明的环境**

**我们可以通过target和lib来决定哪些内置类型声明是可以使用的：**

例如，startsWith字符串方法只能从称为ECMAScript 6的 JavaScript 版本开始使用；

**我们可以通过target的编译选项来配置：TypeScript通过lib根据您的target设置更改默认包含的文件来帮助解决此问题。**

https://www.typescriptlang.org/tsconfig#lib

#### 52.外部定义类型声明和自定义声明

外部类型声明通常是我们使用一些库（比如第三方库）时，需要的一些类型声明

这些库通常有两种类型声明方式：

 方式一：在自己库中进行类型声明（编写.d.ts文件），比如axios

方式二：通过社区的一个公有库DefinitelyTyped存放类型声明文件

该库的GitHub地址：<https://github.com/DefinitelyTyped/DefinitelyTyped/>

该库查找声明安装方式的地址：<https://www.typescriptlang.org/dt/search?search>=

比如我们安装react的类型声明： npm i @types/react --save-dev

 什么情况下需要自己来定义声明文件呢？

情况一：我们使用的第三方库是一个纯的JavaScript库，没有对应的声明文件；比如lodash

情况二：我们给自己的代码中声明一些类型，方便在其他地方直接进行使用；

```ts
declare module "lodash" {
  export function join(...args: any[]): any
}

// 为自己的 变量/函数/类 定义类型声明
declare const whyName: string
declare const whyAge: number
declare const whyHeight: number

declare function foo(bar: string): string

declare class Person {
  constructor(public name: string, public age: number)
}

// 作为一个第三方库为其他开发者提供类型声明文件 .d.ts => axios.d.ts


// 声明文件模块
declare module "*.png"
declare module "*.jpg"
declare module "*.jpeg"
declare module "*.svg"

declare module "*.vue"


// 声明成模块(不合适)
// 声明命名空间
declare namespace $ {
  export function ajax(settings: any): any
}

```

