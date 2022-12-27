---
title: lodash
date: 2022-12-26 23:11:45
tags:
- [lodash]
categories:
- [javascript,lodash]
---

## `“Array” Methods`

### `_.chunk(array, [size=1])`

这个方法可以将一位数组转化成二维数组，第二个参数是指每一个子数组中的元素个数

#### Arguments

1. `array` *(Array)*: The array to process.
2. `[size=1]` *(number)*: The length of each chunk

#### Returns

*(Array)*: Returns the new array of chunks.

```js
_.chunk(['a', 'b', 'c', 'd'], 2);
// => [['a', 'b'], ['c', 'd']]
 
_.chunk(['a', 'b', 'c', 'd'], 3);
// => [['a', 'b', 'c'], ['d']]
```

解析

```js
function chunk(array, size = 1) {
  //和0进行比较，避免size出现负数
  size = Math.max(toInteger(size), 0)
  //获取length
  const length = array == null ? 0 : array.length
  //避免length不存在或者为0
  if (!length || size < 1) {
    //直接返回空数组
    return []
  }
  let index = 0
  let resIndex = 0
  //需要定义多少个chunk
  const result = new Array(Math.ceil(length / size))
	//while循环
  while (index < length) {
    result[resIndex++] = slice(array, index, (index += size))
  }
  return result
}
```

### `_.compact(array)`

返回一个新的数组，删除掉为falsey的value，例如false,NaN,undefined,"",0,null,

#### Arguments

1. `array` *(Array)*: The array to compact.

#### Returns

*(Array)*: Returns the new array of filtered values.

例如：

```js
_.compact([0, 1, false, 2, '', 3]);
// => [1, 2, 3]
```

源码

```js
function compact(array) {
  let resIndex = 0
  const result = []

  if (array == null) {
    return result
  }

  for (const value of array) {
    if (value) {
      result[resIndex++] = value
    }
  }
  return result
}
```

### `_.concat(array, [values])`

将多个值连接成一个新的数组

#### Arguments

1. `array` *(Array)*: The array to concatenate.
2. `[values]` *(...\*)*: The values to concatenate.

#### Returns

*(Array)*: Returns the new concatenated array.

#### Example

```js
var array = [1];
var other = _.concat(array, 2, [3], [[4]]);
 
console.log(other);
// => [1, 2, 3, [4]]
 
console.log(array);
// => [1]
```

#### source

```js
function concat() {
      var length = arguments.length;
      if (!length) {
        return [];
      }
      var args = Array(length - 1),
          array = arguments[0],
          index = length;

      while (index--) {
        args[index - 1] = arguments[index];
      }
      return arrayPush(isArray(array) ? copyArray(array) : [array], baseFlatten(args, 1));
    }
```

### `_.difference(array, [values])`

比较两个数组之间的差异，返回一个新的数组，包含第一个数组的值但不含另外一个数组

#### Arguments

1. `array` *(Array)*: The array to inspect.
2. `[values]` *(...Array)*: The values to exclude.

#### Returns

*(Array)*: Returns the new array of filtered values.

#### Example

```
_.difference([2, 1], [2, 3]);
// => [1]
```

#### source

```js
function difference(array, ...values) {
  //判断是否是数组或者类数组
  return isArrayLikeObject(array)
    ? baseDifference(array, baseFlatten(values, 1, isArrayLikeObject, true))
    : []
}
```

### `_.differenceBy(array, [values], [iteratee=_.identity])`

这个方法和第一个difference类似，但是她可以穿入一个方法，数组的每个值都会执行这个方法，并返回一个新的数组，执行完iteratee，过滤第一个·数组，不包含第二个数组

#### Arguments

1. `array` *(Array)*: The array to inspect.
2. `[values]` *(...Array)*: The values to exclude.
3. `[iteratee=_.identity]` *(Function)*: The iteratee invoked per element.

#### Returns

*(Array)*: Returns the new array of filtered values.

#### Example

```js
_.differenceBy([2.1, 1.2], [2.3, 3.4], Math.floor);
// => [1.2]
 
// The `_.property` iteratee shorthand.
_.differenceBy([{ 'x': 2 }, { 'x': 1 }], [{ 'x': 1 }], 'x');
// => [{ 'x': 2 }]
```

#### source

```js
function differenceBy(array, ...values) {
  let iteratee = last(values)
  if (isArrayLikeObject(iteratee)) {
    iteratee = undefined
  }
  return isArrayLikeObject(array)
    ? baseDifference(array, baseFlatten(values, 1, isArrayLikeObject, true), iteratee)
    : []
}
```

### `_.join(array, [separator=','])`

将数组转化成字符串，以第二个参数作为拼接

#### Arguments

1. `array` *(Array)*: The array to convert.
2. `[separator=',']` *(string)*: The element separator.

#### Returns

*(string)*: Returns the joined string.

#### Example

```
_.join(['a', 'b', 'c'], '~');
// => 'a~b~c'
```

#### source

```js
   function join(array, separator) {
     //nativeJoin===Array.prototype.join
      return array == null ? '' : nativeJoin.call(array, separator);
    }
```

### `_.last(array)`

#### Arguments

1. `array` *(Array)*: The array to query.

#### Returns

*(\*)*: Returns the last element of `array`.

#### Example

```js
_.last([1, 2, 3]);
// => 3
```

#### source

```js
function last(array) {
  const length = array == null ? 0 : array.length
  return length ? array[length - 1] : undefined
}
```

## `“Lang” Methods`

### `_.castArray(value)`

### `_.clone(value)`

浅拷贝

此方法大致基于结构化克隆算法，支持arrays, array buffers, booleans, date objects,、Maps、number、object、正则表达式、Set、字符串、 symbols, and typed arrays。 arguments 对象自己的可枚举属性被克隆为普通对象。对于错误对象、函数、DOM 节点和 WeakMaps 等不可克隆的值，将返回一个空对象。

#### Arguments

1. `value` *(\*)*: The value to clone.

#### Returns

*(\*)*: Returns the cloned value.

#### Example

```js
var objects = [{ 'a': 1 }, { 'b': 2 }];
 
var shallow = _.clone(objects);
console.log(shallow[0] === objects[0]);
// => true
```

#### source

```js


/** Used to compose bitmasks for cloning. */
const CLONE_SYMBOLS_FLAG = 4

function clone(value) {
  return baseClone(value, CLONE_SYMBOLS_FLAG)
}

```

