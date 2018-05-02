---
title: React学习笔记——表单(v15.5)
date: 2017-04-24
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

React中的HTML表单元素有点不同于其它DOM元素，因为表单元素天生具备一些内部状态。

```html
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

<!--more-->

这个表单具有HTML默认行为，当用户提交表单后跳转到一个新页面。如果你想要在React中实现这种行为，它是有效的。但是，大多数情况是，使用一个JavaScript函数来处理表单提交以及对用户输入表单的数据操作是很方便的。实现这一标准的方法是一种称为“控制组件”的技术。

### 控制组件

在HTML表单元素中，诸如\<input>、 \<textarea> 和 \<select>元素，通常保持着它们自己的状态并且根据用户的输入而更新。在React中可变的状态通常保存在组件的state属性中，并且只能使用setState()来更新它们。

我们可以这两个结合起来，使React state成为单一数据源。然后React组件渲染表单并且控制用户后的输入。这种React控制表单元素值的方法被叫做“控制组件”。

例如，前面示例提交表单记录Name时，我们可以把表单写成一个控制组件：

```jsx
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

由于表单元素上设置有value属性，显示的值永远this.state.value的值。

### textarea标签

在HTML中textarea标签的文本内容写在标签里面

```html
<textarea>
  Hello there, this is some text in a text area
</textarea>
```

但是在React中使用value属性代替

```jsx
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

### 处理多个Input

如果想处理多个input元素，可以给每个元素加上name属性，并让事件处理函数依据event.targe.name选择处理。

```jsx
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

注意，示例18行使用了ES6的计算属性语法，如果用ES5语法实现如下：

```jsx
var partialState = {};
partialState[name] = value;
this.setState(partialState);
```



