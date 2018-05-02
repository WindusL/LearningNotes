---
title: React学习笔记——提升state(v15.5)
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

通常几个组件需要反映相同数据变化，我们推荐提升共享的state到它们最近的共同祖先元素。实现如下：

创建一个温度计算器来计算，给定温度的水是否会沸腾。

我们将从一个BoilingVerdict组件开始，它接收一个celsius作为prop参数，并且打印水温度。

<!--more-->

```jsx
function BoilingVerdict(props) {
  if (props.celsius >= 100) {
    return <p>The water would boil.</p>;
  }
  return <p>The water would not boil.</p>;
}
```

接下来，创建一个Calculator组件，它渲染一个\<input>用来输入一个摄氏温度，并且保存它的值在this.state.temperature之中。

此外，它根据输入的值来渲染BoilingVerdict组件

```jsx
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {temperature: ''};
  }

  handleChange(e) {
    this.setState({temperature: e.target.value});
  }

  render() {
    const temperature = this.state.temperature;
    return (
      <fieldset>
        <legend>Enter temperature in Celsius:</legend>
        <input
          value={temperature}
          onChange={this.handleChange} />
        <BoilingVerdict
          celsius={parseFloat(temperature)} />
      </fieldset>
    );
  }
}
```

添加第二个输入框

我们的新要求是，除了一个摄氏度输入外，再提供一个华氏度输入，并且它们保持同步。

我们可以从Calculator组件提取一个TemperatureInput组件，增加一个scale prop参数，它可的取值可以是“c”或者“f”:

```jsx
const scaleNames = {
  c: 'Celsius',
  f: 'Fahrenheit'
};

class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {temperature: ''};
  }

  handleChange(e) {
    this.setState({temperature: e.target.value});
  }

  render() {
    const temperature = this.state.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature}
               onChange={this.handleChange} />
      </fieldset>
    );
  }
}
```

现在，修改Calculator组件，渲染两个不同的温度输入：

```jsx
class Calculator extends React.Component {
  render() {
    return (
      <div>
        <TemperatureInput scale="c" />
        <TemperatureInput scale="f" />
      </div>
    );
  }
}
```

现在已经实现了两个输入框，接下来实现输入其中一个，另一个同时更新功能。

### 编写转换函数

首先，我们写两个函数用于在摄氏度和华氏度之间的单位转换：

```jsx
function toCelsius(fahrenheit) {
  return (fahrenheit - 32) * 5 / 9;
}

function toFahrenheit(celsius) {
  return (celsius * 9 / 5) + 32;
}
```

这两个是转换数字的函数，我们将另外写一个函数，它接收一个字符串temperature参数和一个converter函数作为参数，并返回一个字符串。我们使用它基于一个输入框的值计算出另一个输入框的值。

当它接收一个无效的temperature参数时返回空字符串，并且它保证输出到小数点后三位。

```jsx
function tryConvert(temperature, convert) {
  const input = parseFloat(temperature);
  if (Number.isNaN(input)) {
    return '';
  }
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  return rounded.toString();
}
```

例如，tryConvert('abc', toCelsius)返回一个空字符串;　tryConvert('10.22', toFahrenheit)　返回　'50.396'。

### 提升state

一般的，TemperatureInput组件的值独立保存到它们的局部state上，然而我们想让两个输入框同步，就需要将共享state移动到需要它的最近共同的祖先组件上。这就叫做“提升state”。

我们将移除TemperatureInput组件局部state到Calculator组件上。

具体步骤：

1.把TemperatureInput组件的　this.state.temperature　换成　this.props.temperature

```jsx
render() {
    // 修改前: const temperature = this.state.temperature;
    const temperature = this.props.temperature;
｝
```

我们知道props是只读的。当temperature在局部state时，可以能setState()修改。然而，现在temperature作为一个prop从父类传过来，TemperatureInput组件无法再控制它。

在React中，通常用组件来解决这种控制问题。就像Dom元素\<input/>接收一个value和一个onChange作为prop参数一样，因此可以自定义TemperatureInput组件接收temperature和onTemperatureChange作为prop参数，从它的父组件Calculator中传过来。

现在当TemperatureInput组件想要更新它的temperature时，它可以调用this.props.onTemperatureChange：

```jsx
handleChange(e) {
    // 修改前: this.setState({temperature: e.target.value});
    this.props.onTemperatureChange(e.target.value);
｝
```

注意此处的temperature 或者 onTemperatureChange仅仅作为prop参数名，没有什么特殊的涵义。我们可以定义任意自己喜欢的名字，像value和onChange这种通用的惯例。

onTemperatureChange和temperature作为prop参数由父组件Calculator提供。它将通过修改局部state时行改变，因此两个input的值被重新呈现。修改后的完整代码：

```jsx
class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(e) {
    this.props.onTemperatureChange(e.target.value);
  }

  render() {
    const temperature = this.props.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature}
               onChange={this.handleChange} />
      </fieldset>
    );
  }
}
```

### 实现Calculator组件

我们会将input里的temperature 和 scale存储到Calulator组件的局部state，这个state是从inputs中提升来的，并且作为它们的数据源。这是我们为了显示两个input值所需要知道的最小条件。

例如，我们摄氏度input中输入37，Calculator组件的state应为

```json
{
temperature: '37',
scale: 'c'
}
```

如果在华氏度的input中输入212，Calculator组件的state应为

```json
{
temperature: '212',
scale: 'f'
}
```

我们可以同时保存两个input的值，但它是不必要的。我们可以根据temperature 和 scale值推断出，其它输入框的值。

两个input保持同步的原因在于，它们的计算来自同一个state：

```jsx
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
    this.state = {temperature: '', scale: 'c'};
  }

  handleCelsiusChange(temperature) {
    this.setState({scale: 'c', temperature});
  }

  handleFahrenheitChange(temperature) {
    this.setState({scale: 'f', temperature});
  }

  render() {
    const scale = this.state.scale;
    const temperature = this.state.temperature;
    const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
    const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;

    return (
      <div>
        <TemperatureInput
          scale="c"
          temperature={celsius}
          onTemperatureChange={this.handleCelsiusChange} />
        <TemperatureInput
          scale="f"
          temperature={fahrenheit}
          onTemperatureChange={this.handleFahrenheitChange} />
        <BoilingVerdict
          celsius={parseFloat(celsius)} />
      </div>
    );
  }
}
```

### 整体流程：

1、React在Dom的\<input/>上通过onChange事件调用指定方法。在上面示例中是TemperatureInput组件中的handleChange方法。

2、方法handleChange中通过输入的值e.target.value调用this.props.onTemperatureChange()方法，它是父组件Calculator传递过来的一个prop。

3、当它被渲染的时候，父组件Calculator已经为TemperatureInput组件onTemperatureChange属性指定了摄氏度handleCelsiusChange和华氏度handleFahrenheitChange方法。它会依据被编辑的输入框，决定调用哪个方法。

4、在这些方法内部，Calculator组件通过input输入的值和scale来调用setState()方法修改state。

5、React调用Calculator组件的render方法，通过当前state，调用转换方法进行温度转换。

6、React在render方法中通过新的prop参数逐个调用TemperatureInput组件。

7、React更新匹配的input值，被编辑的input值保持不变，其它的input同步更新转换后的值。

每次更新都保持相同的步骤，所以input保持同步。


