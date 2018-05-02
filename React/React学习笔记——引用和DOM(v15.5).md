---
title: React学习笔记——引用和DOM(v15.5)
date: 2017-05-03
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

在典型的React数据流中，props是父组件与子组件交互的唯一方式。想要修改子组件的，你需要重新渲染父组件的props。然而，有一些种情况是你需要在典型数据流的外面立即修改子组件。要修改的子组件可以是React组件的实例或者是一个DOM元素。对于这两种情况，React 提供了一个解决方案。

<!--more-->

### 何时使用Refs

> 1、管理焦点、选中或者是媒体重放。  
> 2、触发命令动画。  
> 3、与第三方DOM库集成。  

### 避免在可声明的事物上使用refs。

例如：不在Dialog组件上暴露open()和close()方法，而是传递一个isOpen属性。

### 在DOM元素上添加一个ref

React支持在任何组件上添加一个特殊的属性。ref属性接收一个回调函数，并且这个函数在组件mounted和unmounted时会立即执行。

当ref属性被用在一个HTML元素上时，ref回调函数接收的是基础的DOM元素作为它的参数。

```jsx
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.focus = this.focus.bind(this);
  }

  focus() {
    // 明确文本框使用的原始DOM　API focus
    this.textInput.focus();
  }

  render() {
    // ref回调函数存储文本框DOM
    // element in an instance field (for example, this.textInput).
    return (
      <div>
        <input
          type="text"
          ref={(input) => { this.textInput = input; }} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focus}
        />
      </div>
    );
  }
}
```

当组件mounts时，React会调用ref回调函数，并向其传入DOM元素;当组件unmounts时，React调用ref回调函数，并向其传入null。

使用ref回调函数为类设置一个属性，访问DOM元素是一种常见的方式。首先的方法就是向上面一样在ref回调函数中设置一个属性。有一个更简单的写法是：ref={input => this.textInput = input}

### 在类的组件上添加一个ref

当ref属性被添加到一个以类方式定义的组件上时，它的回调函数接收的是已mount组件的实例作为实例。例如：想要模拟组件在mount后直接被点击：

```jsx
class AutoFocusTextInput extends React.Component {
  componentDidMount() {
    this.textInput.focus();
  }

  render() {
    return (
      <CustomTextInput
        ref={(input) => { this.textInput = input; }} />
    );
  }
}
```

**注意**：只有组件以类的方式定义才有效。

### Refs和函数式组件

不能在函数式组件上使用ref属性，因为函数式组件没有实例

```jsx
function MyFunctionalComponent() {
  return <input />;
}

class Parent extends React.Component {
  render() {
    // This will *not* work!
    return (
      <MyFunctionalComponent
        ref={(input) => { this.textInput = input; }} />
    );
  }
}
```

如果你需要使用它，你应该将组件转换为类，就像你需要使用生命周期或者状态时候一样。

但是，你可以在函数式组件内部使用ref属性，只要是DOM元素或类组件上就可以。

```jsx
function CustomTextInput(props) {
  // textInput must be declared here so the ref callback can refer to it
  let textInput = null;

  function handleClick() {
    textInput.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={(input) => { textInput = input; }} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );  
}
```

### 暴露DOM Refs到父组件

在极少的情况下，你可能想要在一个父组件中接收子组件的DOM节点。这种做法通常是不推荐的，但是对于触发焦点和测试DOM的大小或位置，它偶尔也是有用的。

虽然，可以在子组件上添加一个ref属性，但这不理想的解决方案，你只会得到一个组件的实例，而不是组件的DOM节点。此外，这也不会在函数式组件上工作。

相反，在这种情况下，我们希望暴露一个特殊的prop在子组件上。子组件接收一个任意名字的函数作为prop，并且在一DOM节点上加上一个ref属性。这让父组件通过中间件传递ref回调函数到子组件的DOM节点上。

它在类组件和函数式组件上都可以工作。

```jsx
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

在上面的例子中，结果this.inputElement在Parent组件中被设置为CustomTextInput组件input元素相应的DOM节点。

这种模式的好处是，它可以纵向深入使用。例如：Parent组件不需要DOM节点，但是另一个组件渲染Parent组件的需要DOM节点（我们叫它GrandParent），我们可以在GrandParent组件中传递inputRef到Parent组件，并且让Parent组件向下传递它到CustomTextInput组件。

```jsx
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

function Parent(props) {
  return (
    <div>
      My input: <CustomTextInput inputRef={props.inputRef} />
    </div>
  );
}


class Grandparent extends React.Component {
  render() {
    return (
      <Parent
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

注意，如果此种方法需要在子组件中添加代码，如果你对子组件没有绝对的控制权，那么你只能使用findDOMNode()，但是它是不被鼓励使用的。

### 不要过渡使用Refs

你可能倾向使用ref来解决问题应用中的问题，如果是这种情况，请花费一些时间考虑一下，状态应该在组件的层次结构什么位置。通常，拥有该状态的适当位置应该是更高层级的组件上。参考状态提升示例。

### 旧版API：字符串Refs

如果你之前使用过React，你可能会熟悉一个较旧的API,其中ref属性是一个字符串，如像"textInput",DOM节点被这样访问this.refs.textInput。我们建议不这样使用。因为字符串引用有一些被认为是遗留的问题，并且可能在未来的一个版本中被删除。如果你目前正在使用this.refs.textInput访问refs,我们建议你使用回调模式。

**注意**

如果ref回调被定义为内联函数，它将在更新期间被调用两次，第一次调用传入null,第二次调用传入DOM 元素。这是因为函数的新实例是通过每次渲染创建的，因此React需要清除旧的引用并设置新的引用。你可以通过将ref回调定义为类上的绑定方法来避免这种情况，但请注意，在大多数情况下它不重要。



