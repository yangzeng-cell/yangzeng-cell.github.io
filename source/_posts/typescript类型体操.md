---
title: typescript类型体操
date: 2022-10-23 22:10:59
tags:
- [typescript]
categories:
- [typescript,类型体操]
---

# **内置工具和类型体操**

**类型系统其实在很多语言里面都是有的，比如Java、Swift、C++等等，但是相对来说TypeScript的类型非常灵活：**

这是因为TypeScript的目的是为JavaScript添加一套类型校验系统，因为JavaScript本身的灵活性，也让TypeScript类型系统

不得不增加更附加的功能以适配JavaScript的灵活性；

所以TypeScript是一种可以支持类型编程的类型系统； 

**这种类型编程系统为TypeScript增加了很大的灵活度，同时也增加了它的难度：**

如果你不仅仅在开发业务的时候为自己的JavaScript代码增加上类型约束，那么基本不需要太多的类型编程能力；

但是如果你在开发一些框架、库，或者通用性的工具，为了考虑各种适配的情况，就需要使用类型编程； 

**TypeScript本身为我们提供了类型工具，帮助我们辅助进行类型转换（前面有用过关于this的类型工具）。**

**很多开发者为了进一步增强自己的TypeScript编程能力，还会专门去做一些类型体操的题目：**

https://github.com/type-challenges/type-challenges

https://ghaiklor.github.io/type-challenges-solutions/en/

### **条件类型（Conditional Types）**

很多时候，日常开发中我们需要基于输入的值来决定输出的值，同样我们也需要基于输入的值的类型来决定输出的值的类型

**条件类型（**Conditional types）就是用来帮助我们描述输入类型和输出类型之间的关系。

```ts
interface Animal {
  live(): void;
}
interface Dog extends Animal {
  woof(): void;
}
 
type Example1 = Dog extends Animal ? number : string;

 
type Example2 = RegExp extends Animal ? number : string;

```



```ts

// 举个栗子: 函数的重载
 function sum(num1: number, num2: number): number
 function sum(num1: string, num2: string): string

// 错误的做法: 类型扩大化
 function sum(num1: string|number, num2: string|number): string

//正确的写法
function sum<T extends number | string>(num1: T, num2: T): T extends number? number:string
function sum(num1, num2) {
  return num1 + num2
}

```

条件类型看起来有点像 JavaScript 中的条件表达式（`条件 ? true 表达式 : false 表达式`）：

```js
 SomeType extends OtherType ? TrueType : FalseType;
```

#### 条件类型约束

通常，条件类型的检查将为我们提供一些新信息。 就像使用类型守卫缩小范围可以给我们提供更具体的类型一样，条件类型的 true 分支将根据我们检查的类型进一步约束泛型。

让我们来看看下面的例子：

```
type MessageOf<T> = T["message"];
Type '"message"' cannot be used to index type 'T'.Type '"message"' cannot be used to index type 'T'.
```

在本例中，TypeScript 产生错误是因为不知道 `T` 有一个名为 `message` 的属性。 我们可以约束 `T`，TypeScript 也不会再抱怨了：

```ts
type MessageOf<T extends { message: unknown }> = T["message"];
 
interface Email {
  message: string;
}
 
interface Dog {
  bark(): void;
}
 
type EmailMessageContents = MessageOf<Email>;
```

然而，如果我们希望 `MessageOf` 接受任何类型，并且在 `message` 属性不可用的情况下默认为 `never` 之类的类型，我们应该怎么做呢？ 我们可以通过移出约束并引入条件类型来实现这一点：

```
type MessageOf<T> = T extends { message: unknown } ? T["message"] : never;
 
interface Email {
  message: string;
}
 
interface Dog {
  bark(): void;
}
 
type EmailMessageContents = MessageOf<Email>;
 
type DogMessageContents = MessageOf<Dog>;
```

在 true 分支中，TypeScript 知道 `T` *将* 有一个 `message` 属性。

作为另一个示例，我们还可以编写一个名为 `Flatten` 的类型，它将数组类型扁平为它们的元素类型，但在其他情况下不会处理它们：

```ts
type Flatten<T> = T extends any[] ? T[number] : T;
 
// Extracts out the element type.
type Str = Flatten<string[]>;//type Str = string
 
// Leaves the type alone.
type Num = Flatten<number>;//number
```

#### **在条件类型中推断（infer）**

我们发现自己使用条件类型来应用约束，然后提取出类型。 这最终成为一种非常常见的操作，条件类型使其变得更容易。

条件类型为我们提供了一种使用 `infer` 关键字从 true 分支中与之进行比较的类型中进行推断的方法。 例如，我们可以在 `Flatten` 中推断元素类型，而不是使用索引访问类型“手动”提取它：

```
type Flatten<Type> = Type extends Array<infer Item> ? Item : Type;
```

在这里，我们使用 `infer` 关键字以声明方式引入一个名为 `Item` 的新泛型类型变量，而不是指定如何在 true 分支中检索元素类型 `T`。 这使我们不必考虑如何挖掘和探索我们感兴趣的类型的结构。

```ts
type CalcFnType = (num1: number, num2: string) => number

function foo() {
  return "abc"
}

// 总结类型体操题目: MyReturnType：推断返回值  传入的值必须是函数 
type MyReturnType<T extends (...args: any[]) => any> = T extends (...args: any[]) => infer R? R: never
//推断参数类型
type MyParameterType<T extends (...args: any[]) => any> = T extends (...args: infer A) => any? A: never


// 获取一个函数的返回值类型: 内置工具 returntypeß
type CalcReturnType = MyReturnType<CalcFnType>
type FooReturnType = MyReturnType<typeof foo>
// type FooReturnType2 = MyReturnType<boolean>

type CalcParameterType = MyParameterType<CalcFnType>

export {}

```

我们可以使用 `infer` 关键字编写一些有用的助手类型别名。 例如，对于简单的情况，我们可以从函数类型中提取返回类型：

```ts
type GetReturnType<Type> = Type extends (...args: never[]) => infer Return
  ? Return
  : never;
 
type Num = GetReturnType<() => number>;//type Num = number
 
type Str = GetReturnType<(x: string) => string>;//type Str = string
 
type Bools = GetReturnType<(a: boolean, b: boolean) => boolean[]>;//type Bools = boolean[]
```

#### **分发条件类型（Distributive Conditional Types）**

**当在泛型中使用条件类型的时候，如果传入一个联合类型，就会变成** **分发的（**distributive****）

```ts
type toArray<T> = T extends any? T[]: never

// number[]|string[]


type NumArray = toArray<number>

// number[]|string[] 而不是 (number|string)[]
type NumAndStrArray = toArray<number|string>
```

通常，分布性是所需的行为。 要避免这种行为，可以用方括号括起 `extends` 关键字的两边。

```
type ToArrayNonDist<Type> = [Type] extends [any] ? Type[] : never;
 
// 'StrOrNumArr' 不再是一个联合类型
type StrOrNumArr = ToArrayNonDist<string | number>;//type StrOrNumArr = (string | number)[]
```

### Partial<Type>

**用于构造一个Type下面的所有属性都设置为可选的类型**

```ts
interface IKun {
  name: string
  age: number
  slogan?: string
}

// 类型体操
type HYPartial<T> = {
  [P in keyof T]?: T[P] 
}


// IKun都变成可选的
type IKunOptional = HYPartial<IKun>
```

### **Required<Type>**

用于构造一个Type下面的所有属性全都设置为必填的类型，这个工具类型跟 Partial 相反。

```ts
interface IKun {
  name: string
  age: number
  slogan?: string
}

// 类型体操
type HYRequired<T> = {
  [P in keyof T]-?: T[P] 
}


// IKun都变成可选的
type IKun2 = Required<IKun>

```

### **Readonly<Type>**

**用于构造一个Type下面的所有属性全都设置为只读的类型，意味着这个类型的所有的属性全都不可以重新赋值**

```ts
interface IKun {
  name: string
  age: number
  slogan?: string
}

// 类型体操
type HYReadonly<T> = {
  readonly [P in keyof T]: T[P] 
}


// IKun都变成可选的
type IKun2 = HYReadonly<IKun>

```

### **Record<Keys, Type>**

**用于构造一个对象类型，它所有的key(键)都是Keys类型，它所有的value(值)都是Type类型**

```ts
interface IKun {
  name: string;
  age: number;
  slogan?: string;
}

// 类型体操
// name | age | slogan
type keys = keyof IKun; //keyof 会变成联合类型
type Res = keyof any; // => number|string|symbol type Res = string | number | symbol

// 确实keys一定是可以作为key的联合类型 keyof any=>string | number | symbol 一定可以作为对象的key
type HYRecord<Keys extends keyof any, T> = {
  [P in Keys]: T;
};

// IKun都变成可选的
type t1 = "上海" | "北京" | "洛杉矶";
type IKuns = HYRecord<t1, IKun>;

const ikuns: IKuns = {
  上海: {
    name: "xxx",
    age: 10,
  },
  北京: {
    name: "yyy",
    age: 5,
  },
  洛杉矶: {
    name: "zzz",
    age: 3,
  },
};
```

### **Pick<Type, Keys>**

用于构造一个类型，它是从Type类型里面挑了一些属性Keys

```ts
interface IKun {
  name: string
  age: number
  slogan?: string
}

// 确实keys一定是可以作为key的联合类型
type HYPick<T, K extends keyof T> = {
  [P in K]: T[P]
}

// IKun都变成可选的
type IKuns = HYPick<IKun, "slogan"|"name">
```

### **Omit<Type, Keys>**

**用于构造一个类型，它是从Type类型里面过滤了一些属性Keys**

```ts
interface IKun {
  name: string
  age: number
  slogan?: string
}

// 确实keys一定是可以作为key的联合类型
type HYOmit<T, K extends keyof T> = {
  [P in keyof T as P extends K ? never: P]: T[P]
}

// IKun都变成可选的
type IKuns = HYOmit<IKun, "slogan"|"name">


export {}

```

### **Exclude<UnionType, ExcludedMembers>**

从联合类型中排除一些类型

```
type IKun = "sing" | "dance" | "rap"

// 确实keys一定是可以作为key的联合类型
type HYExclude<T, E> = T extends E? never: T

// IKun都变成可选的
type IKuns = HYExclude<IKun, "rap">

```



```ts
type T0 = Exclude<"a" | "b" | "c", "a">;//type T0 = "b" | "c"
type T1 = Exclude<"a" | "b" | "c", "a" | "b">;//type T1 = "c"
type T2 = Exclude<string | number | (() => void), Function>;//type T2 = string | number
```

### **Extract<Type, Union>**

**用于构造一个类型，它是从Type类型里面提取了所有可以赋给Union的类型**

```ts
type T0 = Extract<"a" | "b" | "c", "a" | "f">;//type T0 = "a"
type T1 = Extract<string | number | (() => void), Function>;//type T1 = () => void
```

```ts
type IKun = "sing" | "dance" | "rap"

// 确实keys一定是可以作为key的联合类型
type HYExtract<T, E> = T extends E? T: never

// IKun都变成可选的
type IKuns = HYExtract<IKun, "rap"|"dance">
```

### **NonNullable<Type>**

**用于构造一个类型，这个类型从Type中排除了所有的null、undefined的类型**

```ts
type IKun = "sing" | "dance" | "rap" | null | undefined

// 确实keys一定是可以作为key的联合类型
type HYNonNullable<T> = T extends null|undefined ? never: T

// IKun都变成可选的
type IKuns = HYNonNullable<IKun>
```

```ts
type T0 = NonNullable<string | number | undefined>;//type T0 = string | number
type T1 = NonNullable<string[] | null | undefined>;//type T1 = string[]
```

### **ReturnType<Type>**

**用于构造一个含有Type函数的返回值的类型**

```ts
declare function f1(): { a: number; b: string };
 
type T0 = ReturnType<() => string>;
     
//type T0 = string
type T1 = ReturnType<(s: string) => void>;
     
//type T1 = void
type T2 = ReturnType<<T>() => T>;
     
//type T2 = unknown
type T3 = ReturnType<<T extends U, U extends number[]>() => T>;
     
//type T3 = number[]
type T4 = ReturnType<typeof f1>;
     
type T4 = {
    a: number;
    b: string;
}
type T5 = ReturnType<any>;
     
//type T5 = any
type T6 = ReturnType<never>;
     
//type T6 = never
type T7 = ReturnType<string>;
//Type 'string' does not satisfy the constraint '(...args: any) => any'.

```

### **InstanceType<Type>**

**用于构造一个由所有Type的构造函数的实例类型组成的类型**

```ts
class Person {}
class Dog {}

// 类型体操
type HYInstanceType<T extends new (...args: any[]) => any> = T extends new (
  ...args: any[]
) => infer R
  ? R
  : never;

const p1: Person = new Person();

// typeof Person: 构造函数具体的类型
// InstanceType构造函数创建出来的实例对象的类型
type HYPerson = HYInstanceType<typeof Person>;
const p2: HYPerson = new Person();

// 构造函数的例子
// 通过的创建实例的工具函数时会用到这个InstanceType T必须是一个构造函数
function factory<T extends new (...args: any[]) => any>(
  ctor: T
): HYInstanceType<T> {
  return new ctor();
}

const p3 = factory(Person);
const d = factory(Dog);

```

