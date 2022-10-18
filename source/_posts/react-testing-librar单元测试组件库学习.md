---
title: react-testing-library单元测试组件库学习
date: 2022-10-16 23:40:18
tags:
- [react-testing-library]
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

