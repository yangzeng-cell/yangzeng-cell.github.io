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

### `_.cloneDeep(value)`

这个方法类似[`_.clone`](https://www.lodashjs.com/docs/lodash.cloneDeep#clone)，除了它会递归拷贝 `value`。（注：也叫深拷贝）。

#### Arguments

1. `value` *(\*)*: The value to recursively clone.

#### Returns

*(\*)*: Returns the deep cloned value.

#### Example

```js
var objects = [{ 'a': 1 }, { 'b': 2 }];
 
var deep = _.cloneDeep(objects);
console.log(deep[0] === objects[0]);
// => false
```

#### source

```js
/** Used to compose bitmasks for cloning. */
const CLONE_DEEP_FLAG = 1
const CLONE_SYMBOLS_FLAG = 4

/**
 * This method is like `clone` except that it recursively clones `value`.
 * Object inheritance is preserved.
 *
 * @since 1.0.0
 * @category Lang
 * @param {*} value The value to recursively clone.
 * @returns {*} Returns the deep cloned value.
 * @see clone
 * @example
 *
 * const objects = [{ 'a': 1 }, { 'b': 2 }]
 *
 * const deep = cloneDeep(objects)
 * console.log(deep[0] === objects[0])
 * // => false
 */
function cloneDeep(value) {
  return baseClone(value, CLONE_DEEP_FLAG | CLONE_SYMBOLS_FLAG)
}
```

### `_.cloneWith(value, [customizer])`

这个方法和clone方法相似，他会接受一个自定义方法，返回一个clone值，如果customizer是undefuned，则会使用clone方法，customizer方法接受4个参数*(value [, index|key, object, stack])*

#### Arguments

1. `value` *(\*)*: The value to clone.
2. `[customizer]` *(Function)*: The function to customize cloning.

#### Returns

*(\*)*: Returns the cloned value.

#### Example

```js
function customizer(value) {
  if (_.isElement(value)) {
    return value.cloneNode(false);
  }
}
 
var el = _.cloneWith(document.body, customizer);
 
console.log(el === document.body);
// => false
console.log(el.nodeName);
// => 'BODY'
console.log(el.childNodes.length);
// => 0
```

#### source

```js
const CLONE_SYMBOLS_FLAG = 4
function cloneWith(value, customizer) {
  customizer = typeof customizer === 'function' ? customizer : undefined
  return baseClone(value, CLONE_SYMBOLS_FLAG, customizer)
}
```

### `_.cloneDeepWith(value, [customizer])`

cloneDeepWith和cloneWith类似，但是他会进行递归clone value

#### Arguments

1. `value` *(\*)*: The value to recursively clone.
2. `[customizer]` *(Function)*: The function to customize cloning.

#### Returns

*(\*)*: Returns the deep cloned value.

#### Example

```js
function customizer(value) {
  if (_.isElement(value)) {
    return value.cloneNode(true);
  }
}
 
var el = _.cloneDeepWith(document.body, customizer);
 
console.log(el === document.body);
// => false
console.log(el.nodeName);
// => 'BODY'
console.log(el.childNodes.length);
// => 20
```

#### source

```js
function cloneDeepWith(value, customizer) {
  customizer = typeof customizer === 'function' ? customizer : undefined
  return baseClone(value, CLONE_DEEP_FLAG | CLONE_SYMBOLS_FLAG, customizer)
}
```

## `“Function” Methods`

### `_.debounce(func, [wait=0], [options={}])`

创建一个 debounced（防抖动）函数，该函数会从上一次被调用后，延迟 `wait` 毫秒后调用 `func` 方法。 debounced（防抖动）函数提供一个 `cancel` 方法取消延迟的函数调用以及 `flush` 方法立即调用。 可以提供一个 options（选项） 对象决定如何调用 `func` 方法，`options.leading` 与|或 `options.trailing` 决定延迟前后如何触发（注：是 先调用后等待 还是 先等待后调用）。 `func` 调用时会传入最后一次提供给 debounced（防抖动）函数 的参数。 后续调用的 debounced（防抖动）函数返回是最后一次 `func` 调用的结果。

**注意:** 如果 `leading` 和 `trailing` 选项为 `true`, 则 `func` 允许 trailing 方式调用的条件为: 在 `wait` 期间多次调用防抖方法。

如果 `wait` 为 `0` 并且 `leading` 为 `false`, `func`调用将被推迟到下一个点，类似`setTimeout`为`0`的超时。

#### Arguments

1. `func` *(Function)*: 要防抖动的函数。
2. `[wait=0]` *(number)*: 需要延迟的毫秒数。
3. `[options=]` *(Object)*: 选项对象。
4. `[options.leading=false]` *(boolean)*: 指定在延迟开始前调用。
5. `[options.maxWait]` *(number)*: 设置 `func` 允许被延迟的最大值。
6. `[options.trailing=true]` *(boolean)*: 指定在延迟结束后调用。

#### Returns

*(Function)*: Returns the new debounced function.

#### Example

```js
// Avoid costly calculations while the window size is in flux.
jQuery(window).on('resize', _.debounce(calculateLayout, 150));
 
// Invoke `sendMail` when clicked, debouncing subsequent calls.
jQuery(element).on('click', _.debounce(sendMail, 300, {
  'leading': true,
  'trailing': false
}));
 
// Ensure `batchLog` is invoked once after 1 second of debounced calls.
var debounced = _.debounce(batchLog, 250, { 'maxWait': 1000 });
var source = new EventSource('/stream');
jQuery(source).on('message', debounced);
 
// Cancel the trailing debounced invocation.
jQuery(window).on('popstate', debounced.cancel);
```

#### source

```js
import isObject from './isObject.js'
import root from './.internal/root.js'

/**
 * Creates a debounced function that delays invoking `func` until after `wait`
 * milliseconds have elapsed since the last time the debounced function was
 * invoked, or until the next browser frame is drawn. The debounced function
 * comes with a `cancel` method to cancel delayed `func` invocations and a
 * `flush` method to immediately invoke them. Provide `options` to indicate
 * whether `func` should be invoked on the leading and/or trailing edge of the
 * `wait` timeout. The `func` is invoked with the last arguments provided to the
 * debounced function. Subsequent calls to the debounced function return the
 * result of the last `func` invocation.
 *
 * **Note:** If `leading` and `trailing` options are `true`, `func` is
 * invoked on the trailing edge of the timeout only if the debounced function
 * is invoked more than once during the `wait` timeout.
 *
 * If `wait` is `0` and `leading` is `false`, `func` invocation is deferred
 * until the next tick, similar to `setTimeout` with a timeout of `0`.
 *
 * If `wait` is omitted in an environment with `requestAnimationFrame`, `func`
 * invocation will be deferred until the next frame is drawn (typically about
 * 16ms).
 *
 * See [David Corbacho's article](https://css-tricks.com/debouncing-throttling-explained-examples/)
 * for details over the differences between `debounce` and `throttle`.
 *
 * @since 0.1.0
 * @category Function
 * @param {Function} func The function to debounce.
 * @param {number} [wait=0]
 *  The number of milliseconds to delay; if omitted, `requestAnimationFrame` is
 *  used (if available).
 * @param {Object} [options={}] The options object.
 * @param {boolean} [options.leading=false]
 *  Specify invoking on the leading edge of the timeout.
 * @param {number} [options.maxWait]
 *  The maximum time `func` is allowed to be delayed before it's invoked.
 * @param {boolean} [options.trailing=true]
 *  Specify invoking on the trailing edge of the timeout.
 * @returns {Function} Returns the new debounced function.
 * @example
 *
 * // Avoid costly calculations while the window size is in flux.
 * jQuery(window).on('resize', debounce(calculateLayout, 150))
 *
 * // Invoke `sendMail` when clicked, debouncing subsequent calls.
 * jQuery(element).on('click', debounce(sendMail, 300, {
 *   'leading': true,
 *   'trailing': false
 * }))
 *
 * // Ensure `batchLog` is invoked once after 1 second of debounced calls.
 * const debounced = debounce(batchLog, 250, { 'maxWait': 1000 })
 * const source = new EventSource('/stream')
 * jQuery(source).on('message', debounced)
 *
 * // Cancel the trailing debounced invocation.
 * jQuery(window).on('popstate', debounced.cancel)
 *
 * // Check for pending invocations.
 * const status = debounced.pending() ? "Pending..." : "Ready"
 */
function debounce(func, wait, options) {
  let lastArgs,
    lastThis,
    maxWait,
    result,
    timerId,
    lastCallTime

  let lastInvokeTime = 0
  let leading = false
  let maxing = false
  let trailing = true

  // Bypass `requestAnimationFrame` by explicitly setting `wait=0`.
  const useRAF = (!wait && wait !== 0 && typeof root.requestAnimationFrame === 'function')

  if (typeof func !== 'function') {
    throw new TypeError('Expected a function')
  }
  wait = +wait || 0
  if (isObject(options)) {
    //转成boolean
    leading = !!options.leading
    maxing = 'maxWait' in options
    maxWait = maxing ? Math.max(+options.maxWait || 0, wait) : maxWait
    trailing = 'trailing' in options ? !!options.trailing : trailing
  }
  //
  function invokeFunc(time) {
    const args = lastArgs
    const thisArg = lastThis

    lastArgs = lastThis = undefined
    lastInvokeTime = time
    result = func.apply(thisArg, args)
    return result
  }

  function startTimer(pendingFunc, wait) {
    if (useRAF) {
      root.cancelAnimationFrame(timerId)
      return root.requestAnimationFrame(pendingFunc)
    }
    return setTimeout(pendingFunc, wait)
  }

  function cancelTimer(id) {
    if (useRAF) {
      return root.cancelAnimationFrame(id)
    }
    clearTimeout(id)
  }

  function leadingEdge(time) {
    // Reset any `maxWait` timer.
    lastInvokeTime = time
    // Start the timer for the trailing edge.
    timerId = startTimer(timerExpired, wait)
    // Invoke the leading edge.
    return leading ? invokeFunc(time) : result
  }

  function remainingWait(time) {
    const timeSinceLastCall = time - lastCallTime
    const timeSinceLastInvoke = time - lastInvokeTime
    const timeWaiting = wait - timeSinceLastCall

    return maxing
      ? Math.min(timeWaiting, maxWait - timeSinceLastInvoke)
      : timeWaiting
  }

  function shouldInvoke(time) {
    const timeSinceLastCall = time - lastCallTime
    const timeSinceLastInvoke = time - lastInvokeTime

    // Either this is the first call, activity has stopped and we're at the
    // trailing edge, the system time has gone backwards and we're treating
    // it as the trailing edge, or we've hit the `maxWait` limit.
    return (lastCallTime === undefined || (timeSinceLastCall >= wait) ||
      (timeSinceLastCall < 0) || (maxing && timeSinceLastInvoke >= maxWait))
  }

  function timerExpired() {
    const time = Date.now()
    if (shouldInvoke(time)) {
      return trailingEdge(time)
    }
    // Restart the timer.
    timerId = startTimer(timerExpired, remainingWait(time))
  }

  function trailingEdge(time) {
    timerId = undefined

    // Only invoke if we have `lastArgs` which means `func` has been
    // debounced at least once.
    if (trailing && lastArgs) {
      return invokeFunc(time)
    }
    lastArgs = lastThis = undefined
    return result
  }

  function cancel() {
    if (timerId !== undefined) {
      cancelTimer(timerId)
    }
    lastInvokeTime = 0
    lastArgs = lastCallTime = lastThis = timerId = undefined
  }

  function flush() {
    return timerId === undefined ? result : trailingEdge(Date.now())
  }

  function pending() {
    return timerId !== undefined
  }

  function debounced(...args) {
    const time = Date.now()
    const isInvoking = shouldInvoke(time)

    lastArgs = args
    lastThis = this
    lastCallTime = time

    if (isInvoking) {
      if (timerId === undefined) {
        return leadingEdge(lastCallTime)
      }
      if (maxing) {
        // Handle invocations in a tight loop.
        timerId = startTimer(timerExpired, wait)
        return invokeFunc(lastCallTime)
      }
    }
    if (timerId === undefined) {
      timerId = startTimer(timerExpired, wait)
    }
    return result
  }
  debounced.cancel = cancel
  debounced.flush = flush
  debounced.pending = pending
  return debounced
}
```

### `_.throttle(func, [wait=0], [options={}])`

创建一个节流函数，在 wait 秒内最多执行 `func` 一次的函数。 该函数提供一个 `cancel` 方法取消延迟的函数调用以及 `flush` 方法立即调用。 可以提供一个 options 对象决定如何调用 `func` 方法， options.leading 与|或 options.trailing 决定 wait 前后如何触发。 `func` 会传入最后一次传入的参数给这个函数。 随后调用的函数返回是最后一次 `func` 调用的结果。

**注意:** 如果 `leading` 和 `trailing` 都设定为 `true` 则 `func` 允许 trailing 方式调用的条件为: 在 `wait` 期间多次调用。

如果 `wait` 为 `0` 并且 `leading` 为 `false`, `func`调用将被推迟到下一个点，类似`setTimeout`为`0`的超时。

查看[David Corbacho's article](https://css-tricks.com/debouncing-throttling-explained-examples/) 了解[`_.throttle`](https://www.lodashjs.com/docs/lodash.throttle#throttle) 与[`_.debounce`](https://www.lodashjs.com/docs/lodash.throttle#debounce) 的区别。

#### Arguments

1. `func` *(Function)*: 要节流的函数。
2. `[wait=0]` *(number)*: 需要节流的毫秒。
3. `[options=]` *(Object)*: 选项对象。
4. `[options.leading=true]` *(boolean)*: 指定调用在节流开始前。
5. `[options.trailing=true]` *(boolean)*: 指定调用在节流结束后。

#### Returns

*(Function)*: Returns the new throttled function.

#### Example

```js
// Avoid excessively updating the position while scrolling.
jQuery(window).on('scroll', _.throttle(updatePosition, 100));
 
// Invoke `renewToken` when the click event is fired, but not more than once every 5 minutes.
var throttled = _.throttle(renewToken, 300000, { 'trailing': false });
jQuery(element).on('click', throttled);
 
// Cancel the trailing throttled invocation.
jQuery(window).on('popstate', throttled.cancel);
```

#### source

```js
function throttle(func, wait, options) {
  let leading = true
  let trailing = true

  if (typeof func !== 'function') {
    throw new TypeError('Expected a function')
  }
  if (isObject(options)) {
    leading = 'leading' in options ? !!options.leading : leading
    trailing = 'trailing' in options ? !!options.trailing : trailing
  }
  return debounce(func, wait, {
    leading,
    trailing,
    'maxWait': wait
  })
}

export default throttle
```

