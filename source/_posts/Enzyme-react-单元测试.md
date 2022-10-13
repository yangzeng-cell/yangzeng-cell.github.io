---
title: Enzyme react 单元测试
date: 2022-09-25 21:49:48
tags:
categories:
- [单元测试,Enzyme]
---

##

# enyzme不再支持react18，如果需要使用请使用官方的测试工具React Testing Library

**enzyme** / ˈenzaɪm /

Enzyme是一种react的javascript的单元测试工具，可以轻松的测试react组件的输出，Enyzme的API主要是模仿jq的api来进行DOM的遍历和操作

安装方式

```bash
npm i --save-dev enzyme enzyme-adapter-react-17
```

enzyme的配置方式

```js
import Enzyme from 'enzyme';
import Adapter from 'enzyme-adapter-react-17';

Enzyme.configure({ adapter: new Adapter() });
```

Enzyme的三种渲染组件的方式

#### Shallow Rendering

是对官方的Shallow Renderer的封装，将组件渲染成虚拟DOM对象，只会渲染第一层，子组件将不会渲染出来，因此效率非常高，不需要DOM环境，并且可以以JQ的方式访问组件

```jsx
import { shallow } from 'enzyme';

const wrapper = shallow(<MyComponent />);
// ...
```

##### 常见的api

###### `.at(index) => ShallowWrapper` 	

通过索引获取对应的wrapper

```jsx
const wrapper = shallow(<MyComponent />);
expect(wrapper.find(Foo).at(0).props().foo).to.equal('bar');
```

和get方法相似，但是返回值是reactElement不是wrapper

first方法相当于at(0),last方法相当于返回最后一个

###### `.childAt(index) => ShallowWrapper`

通过索引找到第几个子元素，返回wrapper

```jsx
const wrapper = shallow(<ToDoList items={items} />);
expect(wrapper.find('ul').childAt(0).type()).to.equal('li');
```

###### `.children([selector]) => ShallowWrapper`

找到所有子元素，可以传入可选参数，返回wrapper

```jsx
const wrapper = shallow(<ToDoList items={items} />);
expect(wrapper.find('ul').children()).to.have.lengthOf(items.length);
```

###### `.closest(selector) => ShallowWrapper`

返回匹配到的选择器的第一个选择器的wrapper

```jsx
const wrapper = shallow(<MyComponent />);
expect(wrapper.find(Foo).closest('.bar')).to.have.lengthOf(1);
```

#### Full DOM Rendering

完全渲染，它将组件渲染加载成一个真实的DOM节点，用来测试DOM API的交互和组件的生命周期，用到了jsdom来模拟浏览器环境

```jsx
import { mount } from 'enzyme';

const wrapper = mount(<MyComponent />);
```

#### Static Rendered Markup

静态渲染，它将React组件渲染成静态的HTML字符串，然后使用Cheerio这个库解析这段字符串，并返回一个Cheerio的实例对象，可以用来分析组件的html结构

```jsx
import { render } from 'enzyme';

const wrapper = render(<MyComponent />);
// ...
```
