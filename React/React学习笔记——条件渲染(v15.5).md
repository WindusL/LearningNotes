---
title: React学习笔记——条件渲染(v15.5)
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

React条件渲染和JavaScript的条件工作方式是一样的。

- 使用JavaScript中if或者条件运算符来创建一个代表当前状态的元素，然后让React来更新匹配的UI。

<!--more-->

```jsx
//创建登陆用户后组件
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

//创建访客组件
function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}

//创建Greeting组件来动态显示登陆用户或访客组件
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

//渲染Greeting组件
ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);
```

### 元素变量

可以使用变量保存元素，这可有条件的渲染组件的一部分，输出的其余部分保持不变。

```jsx
//创建LoginButton按钮组件
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}
//创建LogoutButton按钮组件
function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}

//创建一个LoginControl组件，它可以根据当前的状态渲染LoginButton或者LogoutButton组件，它也将渲染上面的Gretting组件
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;

    let button = null;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```

### 内联if使用逻辑‘&&’操作符

声明一个变量并且使用if语句有条件的渲染组件是一个很好的方法，然而，有时候你想使用一些简短的语法，就需要用到JSX内联表达式。

JSX可以在‘{}’内嵌套任何表达式，这其中就包括JavaScript逻辑&&操作符，这对于有条件的引入一个元素是很方便的。

```jsx
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```

它之所以起作用是因为true && 表达式　总是得到一个表达式，而false && 表达式 总是计算为false。因此，如果条件是true,&&后面元素将会被输出，相反如果条件是false，React将会忽略并且跳过它。

### 内联if-else条件运算符

另一种内联方式条件渲染元素是使用JavaScript条件运算符：condition ? true : false

```jsx
//使用条件运算符渲染一小块文本
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}

//它也可以使用在大的表达式中，尽管看起来不明显
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}
```

就像JavaScript一样，根据你和团队的考虑，可以选择一种适合风格。但要记住，当一个组件的的条件判断过于复杂时，应该考虑拆分组件了。

### 阻止组件渲染

极少的情况下，你可能一个组件隐藏它自己，即使它被其它组件渲染。想隐藏组件可以把它的返回值设为null。

```jsx
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = {showWarning: true}
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState(prevState => ({
      showWarning: !prevState.showWarning
    }));
  }

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
}

ReactDOM.render(
  <Page />,
  document.getElementById('root')
);
```

组件的render方法返回null，不能影响到组件生命周期方法。例如：componentWillUpdate 和 componentDidUpdate方法仍然后执行。




