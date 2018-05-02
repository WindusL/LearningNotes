---
title: React学习笔记——列表和Keys(v15.5)
date: 2017-04-21
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

### 渲染多个组件

你可以创建一个元素集合，通过‘{}’在JSX中使用

```jsx
//创建元素集合
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);

//使用集合放到ul标签
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```

<!--more-->

### 基础列表组件　

通常希望在一个组件内部渲染列表，修改上面的示例：

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

### Keys

注意上面示例第4行的‘key’，它是一个特殊的字符串属性，当创建一个列表元素是必须要包含它，否则会有如下警告：

a warning that a key should be provided for list items

Keys是帮助React识别哪些项目被修改、添加、删除。主键将给数组中的元素一个稳定的身份。

最好的方式使用一个在列表同级元素中唯一的字符串作为key。例如我们经常使用数据中的IDs作为Keys,但是不建议在没其它办法的情况下使用元素索引作为key,因为如果数据被重新排序，那么对于React来说，识别变动的元素仍然是很慢的。

### 使用Keys提取组件

Keys中有在数组的上下文中才有意义。例如：抽取一个ListItem组件，你应该保持key在数据中的\<ListItem />元素上，而不是在ListItem自己的根元素\<li>上。

```jsx
function ListItem(props) {
  const value = props.value;
  return (
    // 错误！此处不需要key
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 错误！此处应该指定key
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
function ListItem(props) {
  //正确！此处没有key
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 正确！key被放在了数组里面
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

### Keys同级元素间必须是唯一的

如同上面提过的key在数组内部的同级元素间必须唯一，但是并不需要全局唯一，可以在两个不同的数组中使用相同的key:

```jsx
function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
];
ReactDOM.render(
  <Blog posts={posts} />,
  document.getElementById('root')
);
```

> Keys作为React的一个提示服务，不会传递到组件内部。如果需要在组件内部使用，必须明确的使用不同的名字作为pops传入组件：

```jsx
const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);
```

### 在JSX中嵌入map

JSX允许在‘{}’中嵌入任意表达式，因此可以内联使用map()

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```

