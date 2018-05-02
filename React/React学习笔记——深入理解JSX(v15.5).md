---
title: React学习笔记——深入理解JSX(v15.5)
date: 2017-05-02
categories: 
	- 软件开发
tag: 
	- React
	- JavaScript
	- NodeJs
	- HTML
	- React学习笔记
---

[TOC]

从根本上讲，React语法仅仅是React.createElement(component, props, ...children)方法的语法糖。


```jsx
//JSX代码
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>

//编译后：
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
如果标签没有子元素，可以使用闭口标签。

//JSX代码
<div className="sidebar" />

//编译后
React.createElement(
  'div',
  {className: 'sidebar'},
  null
)
```

<!--more-->

### 指定React元素类型
#### react必须在作用域里

由于JSX编译后调用的是React.createElement方法，因此React库必须始终在JSX代码作用域中。

```jsx
import React from 'react';
import CustomButton from './CustomButton';

function WarningButton() {
  // return React.createElement(CustomButton, {color: 'red'}, null);
  return <CustomButton color="red" />;
}
```

如果不使用JavaScript打包工具并且已经使用\<script>标签引入React。它就已经在全局作用域中了。

#### JSX类型使用点表达式

你也可以在JSX中使用点表达式引用组件。如果有一个模块导出很多组件的时候，这是很方便的。例如：MyComponents.DatePicker是一个组件，你可以直接在JSX中使用它：

```jsx
import React from 'react';

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```

#### 用户定义组件必须大写

当一个元素以小写字母开始，它指的就是一个内建组件，如：\<div>或者\<span>，结果就会将'div'或'span'被传入React.createElement方法。而大写字母开始的组件如<Foo/>会编译成React.createElement(Foo)，并且它符合一个组件的定义。

我们推荐使用大写字母定义一个组件。如果你有一个组件是使用小写字母开始，在JSX中使用它前把它赋值给一个大写变量。

例如，下面代码不会如期运行：

```jsx
import React from 'react';

// 错误！这是一个组件应该产首字母大写
function hello(props) {
  // 正确! 这使用的是<div>标签，因为它是一个有效的HTML标签
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // 错误! Reac认为 <hello /> 是一个HTML标签，因为它首字母是小写
  return <hello toWhat="World" />;
}
修复这些问题，重命名hello为Hello，并且在涉及到它的地方使用<Hello/>：

import React from 'react';

// Correct! This is a component and should be capitalized:
function Hello(props) {
  // Correct! This use of <div> is legitimate because div is a valid HTML tag:
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // Correct! React knows <Hello /> is a component because it's capitalized.
  return <Hello toWhat="World" />;
}
```

#### 运行时选择类型

你不能使用普通表达式作为React元素的类型。如果你想使用普通表达式表明元素的类型，首先将其赋值给一个大写变量。这种情况经常出现在你想依据prop渲染不同组件的时候。

```jsx
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 错误! JSX 类型不能使用表达式
  return <components[props.storyType] story={props.story} />;
}
修复这个，首先将其赋值给一个大写变量：

import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 正确! JSX 类型可以使用一个大写变量代替.
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}
```

### JSX的Props
#### JavaScript表达式作为 Props

你可以通过{}传递任何表达式作为prop。例如：

```jsx
<MyComponent foo={1 + 2 + 3 + 4} />
```

if表达式和for循环在JavaScript中不是表达式，因此不能直接在JSX中使用。你可以把它们放在周围代码中：

```jsx
function NumberDescriber(props) {
  let description;
  if (props.number % 2 == 0) {
    description = <strong>even</strong>;
  } else {
    description = <i>odd</i>;
  }
  return <div>{props.number} is an {description} number</div>;
}
```

### 字符串

你可以传递一个字符串作为prop。这两个JSX表达式是等价的：

```jsx
<MyComponent message="hello world" />

<MyComponent message={'hello world'} />
```

当你传递一个字符串时，它的值是未转义的HTML。这两个表达式是等价的：

```jsx
<MyComponent message="&lt;3" />

<MyComponent message={'<3'} />
```

#### Props默认值true

如果你传递一个没有值的prop，它默认传递的是true。这两个表达式是等价的：

```jsx
<MyTextBox autocomplete />

<MyTextBox autocomplete={true} />
```

一般来说，不建议使用默认值，因为ES6的语法中对象简写{foo}等价于{foo:foo}而不是{foo:true}，所以这样写容易混淆。只有这个行为和HTML行相同的时候才能这么使用。

#### 扩展属性

如果你已经有一个props的对象，并且希望这个props传递给JSX中。可以使用...作为扩展运算符传递整个props对象。下面两个组件是相等的：

```jsx
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;
}
```

当你创建通用的容器时，扩展属性是很有用的。然而，它也能使你的代码产品混乱，它很容易使组件产生很多无关紧要的props。我们建议很少使用这个语法。

### JSX子元素
在JSX表达式中包含一个开始标签和一个结束标签，标签中内容通过一个特殊的prop：props.children传递。有几种不同方法传递子元素

#### 字符串

你可以在开始和结束标签之间放一字符串，这样props.children的值就是一个字符串。这对于很多内置的HTML元素来说是很有用的：

```jsx
<MyComponent>Hello world!</MyComponent>
```

#### JSX子元素

你可以提供更多的JSX元素作为子元素，这对于显示嵌套组件是很有用的：

```jsx
<MyContainer>
  <MyFirstComponent />
  <MySecondComponent />
</MyContainer>
```

你可以混合不同类型的子元素，因此你可以使用字符串在JSX子元素中，这是另一种方式的JSX，看起来就像HTML，所以这既是有效的JSX，也是有效的HTML：

```html
<div>
  Here is a list:
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</div>
```

一个React组件不能同时返回多个React元素，但是一个JSX表达式可以有多个子元素。因此，如果你想要一个组件呈现多个事情的时候，你可以像上面一样将它包裹在一个div里。

#### JavaScript表达式作为子元素

你可以通过{}传递任何JavaScript表达式作为子元素。例如：这些表达式是相等的：

```jsx
<MyComponent>foo</MyComponent>

<MyComponent>{'foo'}</MyComponent>
```

这对于渲染任意长度的JSX表达式列表是很有用的。

```jsx
function Item(props) {
  return <li>{props.message}</li>;
}

function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review'];
  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message} />)}
    </ul>
  );
}
```

JavaScript表达式可以和其它类型混合成子元素。这在字符串模板里是很有用的。

```jsx
function Hello(props) {
  return <div>Hello {props.addressee}!</div>;
}
```

#### 函数作为子元素

通常，JavaScript表达式插入到JSX中得到一个字符串，一个React元素或者是那些东西的列表。然而，props.children就像其它prop一样可以传递任何类型的数据，不仅仅是React知道如何渲染的类型。例如，如果你有一个自定义组件，你可以让它带一个回调函数作为props.children：

```jsx
// Calls the children callback numTimes to produce a repeated component
function Repeat(props) {
  let items = [];
  for (let i = 0; i < props.numTimes; i++) {
    items.push(props.children(i));
  }
  return <div>{items}</div>;
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  );
}
```

子元素可以通过自定义组件传递任何类型。

被忽略的Boolean、Null和Undefined

fase、null、undefined和true是有效的子元素。它们只是不渲染。下面JSX表达式呈现的效果是一样的：

```jsx
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>
```

这些在条件渲染React元素的时候是有用的。下面代码，如果showHeader是true则渲染<Header/>组件：

```jsx
<div>
  {showHeader && <Header />}
  <Content />
</div>
```

但请注意，一些“falsy” 值，诸如数字0，仍然会被渲染。例如：下面代码不会得到预期的行为，因为props.messages为空值的时候会打印0

```jsx
<div>
  {props.messages.length &&
    <MessageList messages={props.messages} />
  }
</div>
```

修复上面代码，确保表达式&&前总是布尔值

```jsx
<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>
```

相反地，如果你想要打印出false、true、null或者undefined，你必须先把它转换成一个字符串：

```jsx
<div>
  My JavaScript variable is {String(myVariable)}.
</div>
```




