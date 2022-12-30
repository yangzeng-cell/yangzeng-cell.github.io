---
title: react-testing-library单元测试组件库学习
date: 2022-10-16 23:40:18
tags:
- [react-testing-library,单元测试]
catagories:
- [react,react-testing-library]
---

# React Testing Library

[`React Testing Library`](https://github.com/testing-library/react-testing-library)是在DOM Testing Library的基础上增加了一些API用于React组件测试，如果项目通过create-react-app创建的项目提供了开箱即用的**React Testing Library**.，如果你不是使用creact-react-app创建项目，可以使用例如·npm/yarn

```
npm install --save-dev @testing-library/react
```

```
yarn add --dev @testing-library/react
```

## The problem[](https://testing-library.com/docs/react-testing-library/intro/#the-problem)

如果你想写可持续性的测试为你的组件，作为这个目标的一部分，如果你想避免你的测试包含组件的详细的细节，并且专注于你的测试给你的组件带来信心，你的测试是可维护的，并且重构你的组件并不会破坏测试，和减慢你开发的进度

## This solution[](https://testing-library.com/docs/react-testing-library/intro/#this-solution)

React Testing Library测试库是非常轻量化的解决方案，它提供了基于react-dom和react-dom/test-utils，您的测试与您的软件使用方式越相似，它们能给您的信心就越大。你的测试将使用实际的DOM节点，而不是处理渲染的组件实例，这个工具库将提供函数有助于和用户相同的方式访问DOM，以更贴近用户的使用的方式测试组件，这个库可以作为enzyme的替代品

# Example

```js
import React from 'react'
import {rest} from 'msw'
import {setupServer} from 'msw/node'//用于mock 数据请求
import {render, fireEvent, waitFor, screen} from '@testing-library/react'
import '@testing-library/jest-dom'
import Fetch from '../fetch'
//mock 请求数据的接口，模拟返回的数据
const server = setupServer(
  rest.get('/greeting', (req, res, ctx) => {
    return res(ctx.json({greeting: 'hello there'}))
  }),
)
//建立api 的mock 在所有测试以前
beforeAll(() => server.listen())
//每次测试完后都重置请求
afterEach(() => server.resetHandlers())
//关闭请求
afterAll(() => server.close())

test('loads and displays greeting', async () => {
  render(<Fetch url="/greeting" />)
//模拟用户点击事件
  fireEvent.click(screen.getByText('Load Greeting'))
//等待直到获取请求成功并且组件调用setState和重新渲染
  //waitFor会等待直到回调函数响应成功
  await waitFor(() => screen.getByRole('heading'))
	// assert that the alert message is correct using
// toHaveTextContent, a custom matcher from jest-dom.
  expect(screen.getByRole('heading')).toHaveTextContent('hello there')
  expect(screen.getByRole('button')).toBeDisabled()
})

test('handles server error', async () => {
  //重写测试接口
  server.use(
    rest.get('/greeting', (req, res, ctx) => {
      return res(ctx.status(500))
    }),
  )

  render(<Fetch url="/greeting" />)

  fireEvent.click(screen.getByText('Load Greeting'))

  await waitFor(() => screen.getByRole('alert'))

  expect(screen.getByRole('alert')).toHaveTextContent('Oops, failed to fetch!')
  expect(screen.getByRole('button')).not.toBeDisabled()
})
```

推荐使用[Mock Service Worker](https://github.com/mswjs/msw) 这个库来mock window.fetch API

```js
import React, {useState, useReducer} from 'react'
import axios from 'axios'

const initialState = {
  error: null,
  greeting: null,
}

function greetingReducer(state, action) {
  switch (action.type) {
    case 'SUCCESS': {
      return {
        error: null,
        greeting: action.greeting,
      }
    }
    case 'ERROR': {
      return {
        error: action.error,
        greeting: null,
      }
    }
    default: {
      return state
    }
  }
}

export default function Fetch({url}) {
  const [{error, greeting}, dispatch] = useReducer(
    greetingReducer,
    initialState,
  )
  const [buttonClicked, setButtonClicked] = useState(false)

  const fetchGreeting = async url =>
    axios
      .get(url)
      .then(response => {
        const {data} = response
        const {greeting} = data
        dispatch({type: 'SUCCESS', greeting})
        setButtonClicked(true)
      })
      .catch(error => {
        dispatch({type: 'ERROR', error})
      })

  const buttonText = buttonClicked ? 'Ok' : 'Load Greeting'

  return (
    <div>
      <button onClick={() => fetchGreeting(url)} disabled={buttonClicked}>
        {buttonText}
      </button>
      {greeting && <h1>{greeting}</h1>}
      {error && <p role="alert">Oops, failed to fetch!</p>}
    </div>
  )
}
```

# 和Jest一起使用

### jest28

```
yarn add --dev jest-environment-jsdom
```

jsdom 也不再是默认环境。您可以通过编辑 jest.config.js 全局启用 jsdom：

```js
 module.exports = {
+  testEnvironment: 'jsdom',
   // ... other options ...
 }
```

或者，如果您只在某些测试中需要 jsdom，则可以在需要时使用 docblocks 启用它：

```
/**
 * @jest-environment jsdom
 */
```

### Jest 27

jest27的配置方式

```js
 module.exports = {
+  testEnvironment: 'jest-environment-jsdom',
   // ... other options ...
 }
```

### Jest 24 (or lower) and defaults[](https://testing-library.com/docs/react-testing-library/setup#jest-24-or-lower-and-defaults)

如果使用24或者更低的版本，需要安装，并把这个作为jest的环境

```
yarn add --dev jest-environment-jsdom-fifteen
```

```js
 module.exports = {
+  testEnvironment: 'jest-environment-jsdom-fifteen',
   // ... other options ...
 }
```

# API

React 测试库重新导出 DOM 测试库中的所有内容以及这些方法：

## `render`

render会把内容渲染都document.body容器中

```ts
function render(
  ui: React.ReactElement<any>,//第一个参数是需要渲染的组件或者元素
  options?: {
    /* You won't often use this, expand below for docs on options */
  },
): RenderResult
```

## `render` Options

render函数的第二个参数

### `container`

默认情况下，React 测试库将创建一个 div 并将该 div 附加到 document.body，这就是您的 React 组件将被渲染的地方。如果您通过此选项提供自己的 HTMLElement 容器，它将不会自动附加到 document.body。

例如，如果你想使用table作为容器

```react
const table = document.createElement('table')

const {container} = render(<TableBody {...props} />, {
  container: document.body.appendChild(table),
})
```

### `baseElement`

### `hydrate`

### `legacyRoot`

### `wrapper`

### `queries`

## `render` Result

render函数的返回值

### `...queries`[](https://testing-library.com/docs/react-testing-library/api#queries-1)

render 最重要的特性是来自 DOM 测试库的query会自动返回，其第一个参数绑定到 baseElement，默认为 document.body。

例如

```jsx
const {getByLabelText, queryAllByTestId} = render(<Component />)
```

### `container`

渲染的 React 元素的包含 DOM 节点（使用 ReactDOM.render 渲染）。这是一个div。这是一个常规 DOM 节点，因此您可以调用 container.querySelector 等来检查子节点。

要获取渲染元素的根元素，请使用 container.firstChild。

🚨 If you find yourself using `container` to query for rendered elements then you should reconsider! The other queries are designed to be more resilient to changes that will be made to the component you're testing. Avoid using `container` to query for elements!

### `baseElement`

### `debug`

### `rerender`

如果您测试进行 prop 更新的组件以确保正确更新 props 可能会更好。也就是说，如果您希望在测试中更新渲染组件的 props，则可以使用此函数更新渲染组件的 props。

```jsx
import {render} from '@testing-library/react'

const {rerender} = render(<NumberDisplay number={1} />)

// re-render the same component with different props
rerender(<NumberDisplay number={2} />)
```

### `unmount`

这将导致渲染的组件被卸载。这对于测试当您的组件从页面中移除时会发生什么情况很有用（例如测试您不会让事件处理程序闲置导致内存泄漏）。

```jsx
import {render} from '@testing-library/react'

const {container, unmount} = render(<Login />)
unmount()
// your component has been unmounted and now: container.innerHTML === ''
```

### `asFragment`

返回呈现组件的 DocumentFragment。如果您需要避免实时绑定并查看您的组件如何对事件做出反应，这将很有用。

```jsx
import React, {useState} from 'react'
import {render, fireEvent} from '@testing-library/react'

const TestComponent = () => {
  const [count, setCounter] = useState(0)

  return (
    <button onClick={() => setCounter(count => count + 1)}>
      Click to increase: {count}
    </button>
  )
}

const {getByText, asFragment} = render(<TestComponent />)
const firstRender = asFragment()

fireEvent.click(getByText(/Click to increase/))

// This will snapshot only the difference between the first render, and the
// state of the DOM after the click event.
// See https://github.com/jest-community/snapshot-diff
expect(firstRender).toMatchDiffSnapshot(asFragment())
```

## `cleanup`

卸载使用 render 挂载的 React 树。

请注意，如果您使用的测试框架支持 afterEach 全局并且它被注入到您的测试环境（如 mocha、Jest 和 Jasmine），那么这是自动完成的。如果没有，您将需要在每次测试后进行手动清理。

```jsx
import {cleanup, render} from '@testing-library/react'
import test from 'ava'

test.afterEach(cleanup)

test('renders into document', () => {
  render(<div />)
  // ...
})

```

## `act`

This is a light wrapper around the [`react-dom/test-utils` `act` function](https://reactjs.org/docs/test-utils.html#act). All it does is forward all arguments to the act function if your version of react supports `act`. It is recommended to use the import from `@testing-library/react` over `react-dom/test-utils` for consistency reasons.

## `renderHook`

这是一个带有自定义测试组件的渲染器的便捷包装器。该 API 源自一种流行的测试模式，主要用于发布hook的库。您应该更喜欢渲染，因为自定义测试组件会导致更具可读性和健壮性的测试，因为您要测试的东西并没有隐藏在抽象之后。

因该是用来测试hook

```js
function renderHook<Result, Props>(
  render: (props: Props) => Result,
  options?: RenderHookOptions<Props>,
): RenderHookResult<Result, Props>
```

Example:

```jsx
import {renderHook} from '@testing-library/react'

test('returns logged in user', () => {
  const {result} = renderHook(() => useLoggedInUser())
  expect(result.current).toEqual({name: 'Alice'})
})
```

## `renderHook` Options

### `renderHook` Options `initialProps`

声明在首次调用时传递给渲染回调。如果您在没有道具的情况下调用重新渲染的props，这些将不会被传递。

```jsx
import {renderHook} from '@testing-library/react'

test('returns logged in user', () => {
  const {result, rerender} = renderHook((props = {}) => props, {
    initialProps: {name: 'Alice'},
  })
  expect(result.current).toEqual({name: 'Alice'})
  rerender()
  expect(result.current).toEqual({name: undefined})
})
```

将 renderHook 与 wrapper 和 initialProps 选项结合使用时，initialProps 不会传递给 wrapper 组件。要为包装器组件提供props，请考虑这样的解决方案：

```jsx
const createWrapper = (Wrapper, props) => {
  return function CreatedWrapper({ children }) {
    return <Wrapper {...props}>{children}</Wrapper>;
  };
};

...

{
  wrapper: createWrapper(Wrapper, { value: 'foo' }),
}
```

### `renderHook` Options `wrapper`

See [`wrapper` option for `render`](https://testing-library.com/docs/react-testing-library/api#wrapper)

## `renderHook` Result[](https://testing-library.com/docs/react-testing-library/api#renderhook-result)

### `result`

hooks的返回值

```jsx
import {renderHook} from '@testing-library/react'

const {result} = renderHook(() => {
  const [name, setName] = useState('')
  React.useEffect(() => {
    setName('Alice')
  }, [])

  return name
})

expect(result.current).toBe('Alice')
```

请注意，该值保存在 result.current 中。将 result 视为最近提交的值的引用。

### `rerender`

使用新props渲染先前渲染的渲染回调：就是为之前的回调函数传入新的值



```JSX
import {renderHook} from '@testing-library/react'

const {rerender} = renderHook(({name = 'Alice'} = {}) => name)

// re-render the same hook with different props
rerender({name: 'Bob'})


```

