---
title: React学习笔记——组合与继承(v15.5)
date: 2017-04-25
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

React有一个强大的组合模型，并且推荐组合代替继承，在组件间重用代码。

一些组件不能提前知道它们的子组件。它些问题在一些公共组件尤其明显。如：侧边栏或者弹出框等组件。

我们推荐这样的组件使用prop的特殊属性children，直接传递子元素到它们的输出中。

<!--more-->

```jsx
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}

//传递JSX中嵌套的任意子元素
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```

JSX标签<FancyBorder>内任何内容通过prop children参数传递到FancyBorder组件中。由于，FancyBorder组件把{prop.children}渲染在一个\<div>中，所以传入的元素最终被输入在这里。

虽然这种情况是不常见的，很多时候，我们需要的是在一个组件的多个地方，使用这种嵌套。在这种情况下，我们得自己定义规则来代替使用chidren。

```jsx
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```

React元素像\<Contacts/>和\<Chat/>只是对象，所以可以像其它任何数据一样作为prop传递它们。

有时候，我们认为组件是其它组件的“特殊实例”。例如，我们可能说组件WelcomeDialog是组件Dialog它的特殊实例。

在React中，这也可以通过组合完成。

```jsx
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  );
}
```

组合对于类定义的组件同样有效。

```jsx
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
      {props.children}
    </FancyBorder>
  );
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSignUp = this.handleSignUp.bind(this);
    this.state = {login: ''};
  }

  render() {
    return (
      <Dialog title="Mars Exploration Program"
              message="How should we refer to you?">
        <input value={this.state.login}
               onChange={this.handleChange} />
        <button onClick={this.handleSignUp}>
          Sign Me Up!
        </button>
      </Dialog>
    );
  }

  handleChange(e) {
    this.setState({login: e.target.value});
  }

  handleSignUp() {
    alert(`Welcome aboard, ${this.state.login}!`);
  }
}
```



