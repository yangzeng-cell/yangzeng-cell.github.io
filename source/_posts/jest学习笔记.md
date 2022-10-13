---
title: jest学习笔记
date: 2022-09-16 13:43:47
tags:
categories:
- [单元测试,jest]
---

#### ##Jest安装

```
npm install --save-dev jest
```

#### 常见的匹配器

```js
test('two plus two is four', () => {
  expect(2 + 2).toBe(4);
});
```

`toBe`使用 `Object.is`来进行精准匹配的测试。 如果您想要检查对象的值，请使用 `toEqual` 代替：

```js
test('对象赋值', () => {
  const data = {one: 1};
  data['two'] = 2;
  expect(data).toEqual({one: 1, two: 2});
});
```

`toEqual` 递归检查对象或数组的每个字段

##### 真值

代码中的`undefined`, `null`, and `false`有不同含义，若你在测试时不想区分他们，可以用真值判断。 Jest提供helpers供你使用。

- `toBeNull` 只匹配 `null`
- `toBeUndefined` 只匹配 `undefined`
- `toBeDefined` 与 `toBeUndefined` 相反
- `toBeTruthy` 匹配任何 `if` 语句为真
- `toBeFalsy` 匹配任何 `if` 语句为假

```js
test('null', () => {
  const n = null;
  expect(n).toBeNull();
  expect(n).toBeDefined();
  expect(n).not.toBeUndefined();
  expect(n).not.toBeTruthy();
  expect(n).toBeFalsy();
});

test('zero', () => {
  const z = 0;
  expect(z).not.toBeNull();
  expect(z).toBeDefined();
  expect(z).not.toBeUndefined();
  expect(z).not.toBeTruthy();
  expect(z).toBeFalsy();
});
```

##### 数字

大多数的比较数字有等价的匹配器

```js
test('two plus two', () => {
  const value = 2 + 2;
  expect(value).toBeGreaterThan(3);//大于
  expect(value).toBeGreaterThanOrEqual(3.5);//大于等于
  expect(value).toBeLessThan(5);//小于
  expect(value).toBeLessThanOrEqual(4.5);//小于等于

  // toBe and toEqual are equivalent for numbers
  expect(value).toBe(4);
  expect(value).toEqual(4);
});
```

对于比较浮点数相等，使用 `toBeCloseTo` 而不是 `toEqual`，因为你不希望测试取决于一个小小的舍入误差。

```js
test('两个浮点数字相加', () => {
  const value = 0.1 + 0.2;
  //expect(value).toBe(0.3);           这句会报错，因为浮点数有舍入误差
  expect(value).toBeCloseTo(0.3); // 这句可以运行
});
});
```

##### 字符串

您可以检查对具有 `toMatch` 正则表达式的字符串︰

```js
test('there is no I in team', () => {
  expect('team').not.toMatch(/I/);
});

test('but there is a "stop" in Christoph', () => {
  expect('Christoph').toMatch(/stop/);
});
```

##### 数组和可迭代对象

 `toContain`来检查一个数组或可迭代对象是否包含某个特定项

```js
const shoppingList = [
  'diapers',
  'kleenex',
  'trash bags',
  'paper towels',
  'milk',
];

test('shoppingList数组中包含milk', () => {
  expect(shoppingList).toContain('milk');
  expect(new Set(shoppingList)).toContain('milk');
});
```

##### thorw

你想测试某函数在调用时是否抛出了错误，你需要使用 `toThrow`

```js
function compileAndroidCode() {
  throw new Error('you are using the wrong JDK');
}

test('compiling android goes as expected', () => {
  expect(() => compileAndroidCode()).toThrow();
  expect(() => compileAndroidCode()).toThrow(Error);

  // 你可以自己定义确切的错误消息内容或者使用正则表达式
  expect(() => compileAndroidCode()).toThrow('you are using the wrong JDK');
  expect(() => compileAndroidCode()).toThrow(/JDK/);
});
```

#### 测试异步代码

##### Promise

为你的测试返回一个Promise，则Jest会等待Promise的resove状态 如果 Promise 的状态变为 rejected, 测试将会失败

```js
test('the data is peanut butter', () => {
  return fetchData().then(data => {
    expect(data).toBe('peanut butter');
  });
});
```

##### Async/Await

```js
test('the data is peanut butter', async () => {
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (e) {
    expect(e).toMatch('error');
  }
});
```

你也可以将 `async` and `await`和 `.resolves` or `.rejects`一起使用。

```js
test('the data is peanut butter', async () => {
  await expect(fetchData()).resolves.toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  await expect(fetchData()).rejects.toMatch('error');
});
```

如果期望Promise被Reject，则需要使用 `.catch` 方法。 请确保添加 `expect.assertions` 来验证一定数量的断言被调用。 否则，一个fulfilled状态的Promise不会让测试用例失败。

```js
test('the fetch fails with an error', () => {
  expect.assertions(1);
  return fetchData().catch(e => expect(e).toMatch('error'));
});
```

##### 测试回调函数

```js
test('the data is peanut butter', done => {
  function callback(error, data) {
    if (error) {
      done(error);
      return;
    }
    try {
      expect(data).toBe('peanut butter');
      done();
    } catch (error) {
      done(error);
    }
  }

  fetchData(callback);
});
```

测试回调函数时，需要使用done方法，不能直接传入一个函数，否则没办法测试，需要使用上上面的格式

##### `.resolves` / `.rejects`

您还可以使用 `.resolves` 匹配器在您期望的声明，Jest 会等待这一 Promise 来解决。 如果 Promise 被拒绝，则测试将自动失败。

```
test('the data is peanut butter', () => {
  return expect(fetchData()).resolves.toBe('peanut butter');
});
```

必须要使用return，在 `fetchData` 返回的这个 promise 变更为 resolved 状态、then() 有机会执行之前，测试就已经被视为已经完成了。

如果你希望Promise返回rejected，你需要使用 `.rejects` 匹配器。 它和 `.resolves` 匹配器是一样的使用方式。 如果 Promise 被拒绝，则测试将自动失败。

```
test('the fetch fails with an error', () => {
  return expect(fetchData()).rejects.toMatch('error');
});
```



#### Jest的钩子函数

jest有4个钩子函数

- beforeAll()钩子函数会在所有的测试用例之前执行  只会执行一次
- beforeEach()钩子函数 会在每个测试用例执行之前执行一次，每个测试用例执行前都会执行一次
- afterEach() 钩子函数会在每个测试用里执行完之后执行执行一次
- alfterAll() 钩子函数会在所有用力执行完后执行



#### 生成jest配置文件jest.config.js

```
npx jest --init
```

#### mock函数

Mock 函数允许你测试代码之间的连接——实现方式包括：擦除函数的实际实现、捕获对函数的调用 ( 以及在这些调用中传递的参数) 、在使用 `new` 实例化时捕获构造函数的实例、允许测试时配置返回值。

有两种方法可以模拟函数：要么在测试代码中创建一个 mock 函数，要么编写一个[`手动 mock`](https://jestjs.io/zh-Hans/docs/manual-mocks)来覆盖模块依赖。

##### 使用 mock 函数[](https://jestjs.io/zh-Hans/docs/mock-functions#使用-mock-函数)

假设我们要测试函数 `forEach` 的内部实现，这个函数为传入的数组中的每个元素调用一次回调函数。

```
function forEach(items, callback) {
  for (let index = 0; index < items.length; index++) {
    callback(items[index]);
  }
}
```

为了测试此函数，我们可以使用一个 mock 函数，然后检查 mock 函数的状态来确保回调函数如期调用。

```js
const mockCallback = jest.fn(x => 42 + x);
forEach([0, 1], mockCallback);

// 此 mock 函数被调用了两次
expect(mockCallback.mock.calls.length).toBe(2);

// 第一次调用函数时的第一个参数是 0
expect(mockCallback.mock.calls[0][0]).toBe(0);

// 第二次调用函数时的第一个参数是 1
expect(mockCallback.mock.calls[1][0]).toBe(1);

// 第一次函数调用的返回值是 42
expect(mockCallback.mock.results[0].value).toBe(42);
```

##### `.mock` 属性

所有的 mock 函数都有这个特殊的 `.mock`属性，它保存了关于此函数如何被调用、调用时的返回值的信息。 `.mock` 属性还追踪每次调用时 `this`的值，所以我们同样可以也检视（inspect） `this`

```js
const myMock1 = jest.fn();
const a = new myMock1();
console.log(myMock1.mock.instances);
// > [ <a> ]

const myMock2 = jest.fn();
const b = {};
const bound = myMock2.bind(b);
bound();
console.log(myMock2.mock.contexts);
// > [ <b> ]
```

这些 mock 成员变量在测试中非常有用，用于说明这些 function 是如何被调用、实例化或返回的：

```js
// The function was called exactly once
expect(someMockFunction.mock.calls.length).toBe(1);

// The first arg of the first call to the function was 'first arg'
expect(someMockFunction.mock.calls[0][0]).toBe('first arg');

// The second arg of the first call to the function was 'second arg'
expect(someMockFunction.mock.calls[0][1]).toBe('second arg');

// The return value of the first call to the function was 'return value'
expect(someMockFunction.mock.results[0].value).toBe('return value');

// The function was called with a certain `this` context: the `element` object.
expect(someMockFunction.mock.contexts[0]).toBe(element);

// This function was instantiated exactly twice
expect(someMockFunction.mock.instances.length).toBe(2);

// The object returned by the first instantiation of this function
// had a `name` property whose value was set to 'test'
expect(someMockFunction.mock.instances[0].name).toBe('test');

// The first argument of the last call to the function was 'test'
expect(someMockFunction.mock.lastCall[0]).toBe('test');
```

##### Mock 的返回值[](https://jestjs.io/zh-Hans/docs/mock-functions#mock-的返回值)

Mock 函数也可以用于在测试期间将测试值注入代码︰

```js
const myMock = jest.fn();
console.log(myMock());
// > undefined

myMock.mockReturnValueOnce(10).mockReturnValueOnce('x').mockReturnValue(true);

console.log(myMock(), myMock(), myMock(), myMock());
// > 10, 'x', true, true
```

在函数连续传递风格（functional continuation-passing style）的代码中时，Mock 函数也非常有效。 以这种代码风格有助于避免复杂的中间操作，便于直观表现组件的真实意图，这有利于在它们被调用之前，将值直接注入到测试中。

```js
const filterTestFn = jest.fn();

// Make the mock return `true` for the first call,
// and `false` for the second call
filterTestFn.mockReturnValueOnce(true).mockReturnValueOnce(false);

const result = [11, 12].filter(num => filterTestFn(num));

console.log(result);
// > [11]
console.log(filterTestFn.mock.calls[0][0]); // 11
console.log(filterTestFn.mock.calls[1][0]); // 12
```

大多数现实世界例子中，实际是在依赖的组件上配一个模拟函数并配置它，但手法是相同的。 在这些情况下，尽量避免在非真正想要进行测试的任何函数内实现逻辑。

##### 模拟模块[](https://jestjs.io/zh-Hans/docs/mock-functions#模拟模块)

假定有个从 API 获取用户的类。 该类用 [axios](https://github.com/axios/axios) 调用 API 然后返回 `data`，其中包含所有用户的属性：

users.js

```js
import axios from 'axios';

class Users {
  static all() {
    return axios.get('/users.json').then(resp => resp.data);
  }
}

export default Users;
```

现在，为测试该方法而不实际调用 API (使测试缓慢与脆弱)，我们可以用 `jest.mock(...)` 函数自动模拟 axios 模块。

一旦模拟模块，我们可为 `.get` 提供一个 `mockResolvedValue` ，它会返回假数据用于测试。 实际上，我们想说的是我们想让`axios.get('/users.json')` 有个伪造的响应结果。

users.test.js

```js
import axios from 'axios';
import Users from './users';

jest.mock('axios');

test('should fetch users', () => {
  const users = [{name: 'Bob'}];
  const resp = {data: users};
  axios.get.mockResolvedValue(resp);

  // or you could use the following depending on your use case:
  // axios.get.mockImplementation(() => Promise.resolve(resp))

  return Users.all().then(data => expect(data).toEqual(users));
});
```

##### 模拟 部分模块

模块的子集可以被模拟，模块的其他部分可以维持当前实现：

foo-bar-baz.js

```js
export const foo = 'foo';
export const bar = () => 'bar';
export default () => 'baz';
```

```js
//test.js
import defaultExport, {bar, foo} from '../foo-bar-baz';

jest.mock('../foo-bar-baz', () => {
  const originalModule = jest.requireActual('../foo-bar-baz');

  //Mock the default export and named export 'foo'
  return {
    __esModule: true,
    ...originalModule,
    default: jest.fn(() => 'mocked baz'),
    foo: 'mocked foo',
  };
});

test('should do a partial mock', () => {
  const defaultExportResult = defaultExport();
  expect(defaultExportResult).toBe('mocked baz');
  expect(defaultExport).toHaveBeenCalled();

  expect(foo).toBe('mocked foo');
  expect(bar()).toBe('bar');
});
```

##### Mock 实现[](https://jestjs.io/zh-Hans/docs/mock-functions#mock-实现)

还有，在某些情况下用Mock函数替换指定返回值是非常有用的。 可以用 `jest.fn` 或 `mockImplementationOnce`方法来实现Mock函数。

```javascript
const myMockFn = jest.fn(cb => cb(null, true));

myMockFn((err, val) => console.log(val));
// > true
```



当你需要根据别的模块定义默认的Mock函数实现时，`mockImplementation` 方法是非常有用的。

foo.js

```js
module.exports = function () {
  // some implementation;
};
```



test.js

```js
jest.mock('../foo'); // this happens automatically with automocking
const foo = require('../foo');

// foo is a mock function
foo.mockImplementation(() => 42);
foo();
// > 42
```



当你需要模拟某个函数调用返回不同结果时，请使用 `mockImplementationOnce` 方法︰

```javascript
const myMockFn = jest
  .fn()
  .mockImplementationOnce(cb => cb(null, true))
  .mockImplementationOnce(cb => cb(null, false));

myMockFn((err, val) => console.log(val));
// > true

myMockFn((err, val) => console.log(val));
// > false
```



当 `mockImplementationOne`定义的实现逐个调用完毕时， 如果定义了`jest.fn `，它将使用 `jest.fn `。

```javascript
const myMockFn = jest
  .fn(() => 'default')
  .mockImplementationOnce(() => 'first call')
  .mockImplementationOnce(() => 'second call');

console.log(myMockFn(), myMockFn(), myMockFn(), myMockFn());
// > 'first call', 'second call', 'default', 'default'
```



大多数情况下，我们的函数调用都是链式的，如果你希望创建的函数支持链式调用（因为返回了this），可以使用`.mockReturnThis()` 函数来支持。

```javascript
const myObj = {
  myMethod: jest.fn().mockReturnThis(),
};

// is the same as

const otherObj = {
  myMethod: jest.fn(function () {
    return this;
  }),
};
```

##### 自定义匹配器[](https://jestjs.io/zh-Hans/docs/mock-functions#自定义匹配器)

最后，测试Mock函数需要写大量的断言，为了减少代码量，我们提供了一些自定义匹配器。

```javascript
// The mock function was called at least once
expect(mockFunc).toHaveBeenCalled();

// The mock function was called at least once with the specified args
expect(mockFunc).toHaveBeenCalledWith(arg1, arg2);

// The last call to the mock function was called with the specified args
expect(mockFunc).toHaveBeenLastCalledWith(arg1, arg2);

// All calls and the name of the mock is written as a snapshot
expect(mockFunc).toMatchSnapshot();
```



这些匹配器是断言Mock函数的语法糖。 你可以根据自己的需要自行选择匹配器。

```javascript
// The mock function was called at least once
expect(mockFunc.mock.calls.length).toBeGreaterThan(0);

// The mock function was called at least once with the specified args
expect(mockFunc.mock.calls).toContainEqual([arg1, arg2]);

// The last call to the mock function was called with the specified args
expect(mockFunc.mock.calls[mockFunc.mock.calls.length - 1]).toEqual([
  arg1,
  arg2,
]);

// The first arg of the last call to the mock function was `42`
// (note that there is no sugar helper for this specific of an assertion)
expect(mockFunc.mock.calls[mockFunc.mock.calls.length - 1][0]).toBe(42);

// A snapshot will check that a mock was invoked the same number of times,
// in the same order, with the same arguments. 它还会在名称上断言。 它还会在名称上断言。
expect(mockFunc.mock.calls).toEqual([[arg1, arg2]]);
expect(mockFunc.getMockName()).toBe('a mock name');
```
