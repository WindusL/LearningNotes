---
title: React学习笔记——深入React组件(v15.5)
date: 2017-05-04
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

组件让UI独立，可重复利用，并且单独解决每块问题。React.Component是由React提供的。

React.Component是一个抽象的基类，因此它很少的被直接使用。反而，你会用一个有代表性的子类，并且至少定义一个render()方法。

<!--more-->

使用JavaScript定义一个简单的类组件：

```jsx
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

如果你没有使用ES6，你需要使用react-create-class模块创建。可以参考React中不使用ES6。

### 组件生命周期
每个组件都有几个生命周期，你可重写它们并在特定时间，它们会被执行。前缀是will的方法会在行为发生前被调用;前缀为did的方法会在行为发生后被调用。

#### Mounting

当一个组件实例被创建并且插入到DOM时，这些方法被调用：

> constructor()  
> componentWillMount()  
> render()  
> componentDidMount()

#### Updating

可能由props或state引发的更新。当组件被重新渲染时，这些方法被调用：

> componentWillReceiveProps()  
> shouldComponentUpdate()  
> componentWillUpdate()  
> render()  
> componentDidUpdate()  

#### Unmounting

当组件被移出DOM时，这个方法被调用：

> componentWillUnmount()  

#### 其它API

每个组件也提供一些其它API。

> setState()  
> forceUpdate()  

### 类属性

> defaultProps  
> displayName  

#### 实例属性

> props  
> state  

### 方法使用说明
#### render()

这个方法是必须存在的方法。

当被调用时，它会检查props和state，并且返回单一的React元素。这个元素可以是一个本地DOM组件，如\<div />，也可以是一个自定义的合成组件。

你也可以返回null或者false，表示你不想要任何渲染。当返回的是null或者false时，ReactDOM.findDOMNode(this)返回的是null。

这个方法是纯粹的，也就是说它不能更新state，每次调用返回相同的结果，并且它不直接与浏览器交互。

注意：如果shouldComponentUpdate()方法返回false，render()不会被调用。

#### constructor()

构造函数是初始化state的正确位置。如果你不需要初始化state并且也不绑定方法，那就不需要实现构造函数。

```jsx
constructor(props) {
  super(props);
  this.state = {
    color: props.initialColor
  };
}
```

#### componentWillUpdate()

这个方法在mounting发生前调用，并且在render()方法之前调用。因此，在这个方法设置state同步，不会触发重新渲染。避免了任副作用和订阅。

这仅仅是 服务器渲染时调用的生命周期的钩子。一般推荐使用constructor()代替。

#### componentDidMount()

这个方法mounting发生后调用。在这个方法设置state会触发重新渲染。可以在这里初始化必要的DOM，如果需要加载远程数据，这里是发起请求的好地方。

#### componentWillReceiveProps()

这个方法在一个已加载的组件接收新prop前调用。在这个方法里可以使用setState()比较this.props和nextProps。

注意：即使props没有改变，React也可能调用这个方法。因此，请确保比较当前值和即将改变的值。

在mounting期间初始化props，React不会调用这个方法。它只在组件props被更新时调用。调用setState()通常不会触发此方法。

#### shouldComponentUpdate()

此方法默认行为是，当state改变时组件重新渲染。

当props或state 被接收到时，这个方法将在render()之前调用。当初始化渲染和使用forceUpdate()方法时，此方法不被调用。

此方法返回false时，并不能阻止子组件由于state改变后重新渲染。

目前，此方法返回false时componentWillUpdate()、render() 和 componentDidUpdate()方法不会被调用。注意，这在将来可能只是一个提示，而不是严格的指令，也就是说即使该方法返回false，仍然会重新渲染组件。

#### componentWillUpdate()

此方法被调用在prop和state被接收后组件渲染前，组件初始化渲染不调用此方法。

**注意**：

1、你不能在这个方法中调用this.setState()，如果你想在props变化后更新state请使用componentWillReceiveProps()代替。

2、如果shouldComponentUpdate()方法返回false，此方法不会被调用。

#### componentDidUpdate()

这个方法在updating发生后立即调用，并且在组件初始化渲染时也不调用。

在组件更新后，在这个方法中操作DOM是个很好的机会。这也是一个做网络请求的好地方。

注意：如果shouldComponentUpdate()方法返回false，此方法不会被调用。

#### componentWillUnmount()

此方法会在组件被卸载和销毁之前立即调用。在这个方法可以执行必要的清理工作。如：无效的定时器、取消网络请求或者清理componentDidMount()创建的DOM元素。

#### forceUpdate()

默认情况下，当组件的state或props改变时就会重新渲染。如果你的render()方法需要依赖一此其它数据，你可以通过调用forceUpdate()方法告诉React组件需要重新渲染。

调用forceUpdate()，会引起render()直接调用，而跳过shouldComponentUpdate()。但是它会正常触发每一个子组件的生命周期方法。

通常应该避免使用此方法。





