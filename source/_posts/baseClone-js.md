---
title: baseClone.js
date: 2022-12-28 08:13:22
tags:
- [lodash]
categories:
- [javascript,lodash]
---

```js
import Stack from "./Stack.js";
import arrayEach from "./arrayEach.js";
import assignValue from "./assignValue.js";
import cloneBuffer from "./cloneBuffer.js";
import copyArray from "./copyArray.js";
import copyObject from "./copyObject.js";
import cloneArrayBuffer from "./cloneArrayBuffer.js";
import cloneDataView from "./cloneDataView.js";
import cloneRegExp from "./cloneRegExp.js";
import cloneSymbol from "./cloneSymbol.js";
import cloneTypedArray from "./cloneTypedArray.js";
import copySymbols from "./copySymbols.js";
import copySymbolsIn from "./copySymbolsIn.js";
import getAllKeys from "./getAllKeys.js";
import getAllKeysIn from "./getAllKeysIn.js";
import getTag from "./getTag.js";
import initCloneObject from "./initCloneObject.js";
import isBuffer from "../isBuffer.js";
import isObject from "../isObject.js";
import isTypedArray from "../isTypedArray.js";
import keys from "../keys.js";
import keysIn from "../keysIn.js";

/** Used to compose bitmasks for cloning. */
const CLONE_DEEP_FLAG = 1;
const CLONE_FLAT_FLAG = 2;
const CLONE_SYMBOLS_FLAG = 4;

/** `Object#toString` result references. */
const argsTag = "[object Arguments]";
const arrayTag = "[object Array]";
const boolTag = "[object Boolean]";
const dateTag = "[object Date]";
const errorTag = "[object Error]";
const mapTag = "[object Map]";
const numberTag = "[object Number]";
const objectTag = "[object Object]";
const regexpTag = "[object RegExp]";
const setTag = "[object Set]";
const stringTag = "[object String]";
const symbolTag = "[object Symbol]";
const weakMapTag = "[object WeakMap]";

const arrayBufferTag = "[object ArrayBuffer]";
const dataViewTag = "[object DataView]";
const float32Tag = "[object Float32Array]";
const float64Tag = "[object Float64Array]";
const int8Tag = "[object Int8Array]";
const int16Tag = "[object Int16Array]";
const int32Tag = "[object Int32Array]";
const uint8Tag = "[object Uint8Array]";
const uint8ClampedTag = "[object Uint8ClampedArray]";
const uint16Tag = "[object Uint16Array]";
const uint32Tag = "[object Uint32Array]";

/** Used to identify `toStringTag` values supported by `clone`. */
/**定义那些类型是可以拷贝那些是不可以拷贝的 */
const cloneableTags = {};
cloneableTags[argsTag] =
  cloneableTags[arrayTag] =
  cloneableTags[arrayBufferTag] =
  cloneableTags[dataViewTag] =
  cloneableTags[boolTag] =
  cloneableTags[dateTag] =
  cloneableTags[float32Tag] =
  cloneableTags[float64Tag] =
  cloneableTags[int8Tag] =
  cloneableTags[int16Tag] =
  cloneableTags[int32Tag] =
  cloneableTags[mapTag] =
  cloneableTags[numberTag] =
  cloneableTags[objectTag] =
  cloneableTags[regexpTag] =
  cloneableTags[setTag] =
  cloneableTags[stringTag] =
  cloneableTags[symbolTag] =
  cloneableTags[uint8Tag] =
  cloneableTags[uint8ClampedTag] =
  cloneableTags[uint16Tag] =
  cloneableTags[uint32Tag] =
    true;
cloneableTags[errorTag] = cloneableTags[weakMapTag] = false;

/** Used to check objects for own properties. */
/**用于检测是否是实例对象本身的属性，而不是原型链上的属性 */
const hasOwnProperty = Object.prototype.hasOwnProperty;

/**
 * Initializes an object clone based on its `toStringTag`.
 *
 * **Note:** This function only supports cloning values with tags of
 * `Boolean`, `Date`, `Error`, `Map`, `Number`, `RegExp`, `Set`, or `String`.
 *
 * @private
 * @param {Object} object The object to clone.
 * @param {string} tag The `toStringTag` of the object to clone.
 * @param {boolean} [isDeep] Specify a deep clone.
 * @returns {Object} Returns the initialized clone.
 */
/**根据数据类型初始化创建对应的类型· */
function initCloneByTag(object, tag, isDeep) {
  const Ctor = object.constructor;
  switch (tag) {
    case arrayBufferTag:
      return cloneArrayBuffer(object);

    case boolTag:
    case dateTag:
      return new Ctor(+object);

    case dataViewTag:
      return cloneDataView(object, isDeep);

    case float32Tag:
    case float64Tag:
    case int8Tag:
    case int16Tag:
    case int32Tag:
    case uint8Tag:
    case uint8ClampedTag:
    case uint16Tag:
    case uint32Tag:
      return cloneTypedArray(object, isDeep);

    case mapTag:
      return new Ctor();

    case numberTag:
    case stringTag:
      return new Ctor(object);

    case regexpTag:
      return cloneRegExp(object);

    case setTag:
      return new Ctor();

    case symbolTag:
      return cloneSymbol(object);
  }
}

/**
 * Initializes an array clone.
 *
 * @private
 * @param {Array} array The array to clone.
 * @returns {Array} Returns the initialized clone.
 */
function initCloneArray(array) {
  const { length } = array;
  const result = new array.constructor(length);

  // Add properties assigned by `RegExp#exec`.
  if (
    length &&
    typeof array[0] === "string" &&
    hasOwnProperty.call(array, "index")
  ) {
    result.index = array.index;
    result.input = array.input;
  }
  return result;
}

/**
 * The base implementation of `clone` and `cloneDeep` which tracks
 * traversed objects.
 *
 * @private
 * @param {*} value The value to clone.
 * @param {number} bitmask The bitmask flags.
 *  1 - Deep clone
 *  2 - Flatten inherited properties
 *  4 - Clone symbols
 * @param {Function} [customizer] The function to customize cloning.
 * @param {string} [key] The key of `value`.
 * @param {Object} [object] The parent object of `value`.
 * @param {Object} [stack] Tracks traversed objects and their clone counterparts.
 * @returns {*} Returns the cloned value.
 */
function baseClone(value, bitmask, customizer, key, object, stack) {
  let result;
  const isDeep = bitmask & CLONE_DEEP_FLAG;
  //展平继承的属性
  const isFlat = bitmask & CLONE_FLAT_FLAG;
  const isFull = bitmask & CLONE_SYMBOLS_FLAG;

  //判断是否有传入的处理函数
  if (customizer) {
    result = object ? customizer(value, key, object, stack) : customizer(value);
  }
  if (result !== undefined) {
    return result;
  }
  //如果不是object，直接返回
  if (!isObject(value)) {
    return value;
  }
  //判断是否是数组
  const isArr = Array.isArray(value);
  //获取数据类型
  const tag = getTag(value);
  //如果是数组，对数组进行处理
  if (isArr) {
    result = initCloneArray(value);
    if (!isDeep) {
      return copyArray(value, result);
    }
  } else {
    //是否是function
    const isFunc = typeof value === "function";
    //如果是buffer
    if (isBuffer(value)) {
      return cloneBuffer(value, isDeep);
    }
    //object 或者类数组对象 或者是函数
    if (tag == objectTag || tag == argsTag || (isFunc && !object)) {
      //既不是函数也不需要展开继承属性
      result = isFlat || isFunc ? {} : initCloneObject(value);
      if (!isDeep) {
        return isFlat
          ? copySymbolsIn(value, copyObject(value, keysIn(value), result))
          : copySymbols(value, Object.assign(result, value));
      }
    } else {
      //如果是函数或者是不可复制内容，则返回该值或者空对象
      if (isFunc || !cloneableTags[tag]) {
        return object ? value : {};
      }
      result = initCloneByTag(value, tag, isDeep);
    }
  }
  //检查是否有循环引用，如果有，则返回他的clone
  // Check for circular references and return its corresponding clone.
  stack || (stack = new Stack());
  const stacked = stack.get(value);
  if (stacked) {
    return stacked;
  }
  stack.set(value, result);
  //map类型
  if (tag == mapTag) {
    value.forEach((subValue, key) => {
      result.set(
        key,
        baseClone(subValue, bitmask, customizer, key, value, stack)
      );
    });
    return result;
  }
  //set类型
  if (tag == setTag) {
    value.forEach((subValue) => {
      result.add(
        baseClone(subValue, bitmask, customizer, subValue, value, stack)
      );
    });
    return result;
  }
  //TypedArray类型
  if (isTypedArray(value)) {
    return result;
  }

  const keysFunc = isFull
    ? isFlat
      ? getAllKeysIn
      : getAllKeys
    : isFlat
    ? keysIn
    : keys;

  const props = isArr ? undefined : keysFunc(value);
  arrayEach(props || value, (subValue, key) => {
    if (props) {
      key = subValue;
      subValue = value[key];
    }
    // Recursively populate clone (susceptible to call stack limits).
    assignValue(
      result,
      key,
      baseClone(subValue, bitmask, customizer, key, value, stack)
    );
  });
  return result;
}

```

