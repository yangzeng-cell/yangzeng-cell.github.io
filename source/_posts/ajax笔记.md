---
title: ajax笔记
date: 2022-09-08 13:59:00
tags:
categories:
- [计算机网络,Ajax]
---

### ##ajax的封装

```js
function ajax({
  url,
  method = "get",
  data = {},
  headers = {},
  success,
  failure,
} = {}) {
  // 1.创建对象
  const xhr = new XMLHttpRequest();

  // 2.监听数据

  xhr.onload = function () {
    if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
      // xhr.responseXML/xhr.responseText
      success && success(xhr.response);
    } else {
      failure && failure({ status: xhr.status, message: xhr.statusText });
    }
  };
  // 现在返回的数据类型一般都是json,如果设置为空字符串则会使用text作为默认值
  // 3.设置类型
  xhr.responseType = "json";
  // xhr.responseType="xml"/blob/text/arraybuffer等

  // 4.设置过期超时事件
  xhr.timeout = timeout;
  // 5.open方法
  if (method.toUpperCase() === "GET") {
    const queryString = [];
    for (const key in data) {
      if (Object.hasOwnProperty.call(data, key)) {
        const item = data[key];
        queryString.push(`${key}=${item}`);
      }
    }
    url = url + "?" + queryString.join("&");
    xhr.open(method, url);
  } else {
    // post
    xhr.open(method, url);
    xhr.setRequestHeader("Content-type", "application/json");
  }

  // 6设置headers
  if (Object.keys(headers).length > 0) {
    Object.keys(headers).forEach((key) => {
      xhr.setRequestHeader(key, headers[key]);
    });
  }

  if (method.toUpperCase === "POST") {
    xhr.send(JSON.stringify(data));
  } else if (method.toUpperCase === "GET") {
    xhr.send();
  }
  // return xhr可以用于调用abort()方法进行终止请求
  return xhr;
}

```

#### ajax的promise的封装

```js
function ajax({ url, method = "get", data = {}, headers = {} } = {}) {
  // 1.创建对象
  const xhr = new XMLHttpRequest();
  const promise = new Promise((resolve, reject) => {
    // 2.监听数据

    xhr.onload = function () {
      if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
        // xhr.responseXML/xhr.responseText
        resolve(xhr.response);
      } else {
        reject({ status: xhr.status, message: xhr.statusText });
      }
    };
    // 现在返回的数据类型一般都是json,如果设置为空字符串则会使用text作为默认值
    // 3.设置类型
    xhr.responseType = "json";
    // xhr.responseType="xml"/blob/text/arraybuffer等

    // 4.设置过期超时事件
    xhr.timeout = timeout;
    // 5.open方法
    if (method.toUpperCase() === "GET") {
      const queryString = [];
      for (const key in data) {
        if (Object.hasOwnProperty.call(data, key)) {
          const item = data[key];
          queryString.push(`${key}=${item}`);
        }
      }
      url = url + "?" + queryString.join("&");
      xhr.open(method, url);
    } else {
      // post
      xhr.open(method, url);
      xhr.setRequestHeader("Content-type", "application/json");
    }

    // 6设置headers
    if (Object.keys(headers).length > 0) {
      Object.keys(headers).forEach((key) => {
        xhr.setRequestHeader(key, headers[key]);
      });
    }

    if (method.toUpperCase === "POST") {
      xhr.send(JSON.stringify(data));
    } else if (method.toUpperCase === "GET") {
      xhr.send();
    }
  });

  // return xhr可以用于调用abort()方法进行终止请求

  promise.xhr = xhr;
  return promise;
}

```

