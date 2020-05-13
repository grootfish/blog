---
title: 在 React 中使用计算属性
tags: [javascript,React]
categories: [React]
description: 在 React 中使用计算属性
---
## 在 React 中使用计算属性

### 前言

初次见到计算属性一词是在 Vue 官方文档-[计算属性和侦听器](https://cn.vuejs.org/v2/guide/computed.html)一节中。

> 模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护。

回想我们编写的 React 代码，是否也在 JSX（render 函数）中放入了太多的逻辑导致 `render` 函数过于庞大,难以维护？

### React 中的计算属性

在 Vue 中计算属性有以下两点主要特性

1.  **计算属性以声明的方式创建依赖关系,依赖的 data 或 props 变更会触发重新计算并自动更新；**
2.  **计算属性是基于它们的响应式依赖进行缓存的；**

其实在 React 中计算属性随处可见，相信各位使用过 React 的读者都写过类似的代码

```javascript
import React, { Fragment, Component } from 'react';

class Example extends Component {
  state = {
    firstName: '',
    lastName: '',
  };

  render() {
    // 在 render 函数中处理逻辑
    const { firstName, lastName } = this.state;
    const fullName = `${firstName} ${lastName}`;
    return <Fragment>{fullName}</Fragment>;
  }
}
```

在上面的代码里，render 函数里面的`fullName` 依赖了`props` 中的`firstName`和`lastName` 。`firstName` 或`lastName` 变更之后变量 `fullName` 都会自动更新。其实现原理是**props 以及 state 的变化会导致 render 函数调用，进而重新计算衍生值。**

但是现在我们还是把计算逻辑放入了 render 函数中，更好的做法是把计算逻辑抽出来，简化 render 函数逻辑。

```javascript
class Example extends Component {
  state = {
    firstName: '',
    lastName: '',
  };

  // 把 render 中的逻辑抽成函数,减少render函数的臃肿
  renderFullName() {
    const { firstName, lastName } = this.state;
    return `${firstName} ${lastName}`;
  }

  render() {
    const fullName = this.renderFullName();
    return <Fragment>{fullName}</Fragment>;
  }
}
```

如果你了解 Vue 的话，那么你知道其中的 computed 计算属性，它的底层是使用了getter，只不过是对象的 getter，那么在 React 中我们也可以使用类的 getter 来实现计算属性。

```javascript
class Example extends Component {
  state = {
    firstName: '',
    lastName: '',
  };

  // 通过getter而不是函数形式，减少变量
  get fullName() {
    const { firstName, lastName } = this.state;
    return `${firstName} ${lastName}`;
  }

  render() {
    return <Fragment>{this.fullName}</Fragment>;
  }
}
```

### 使用 memoization 优化计算属性

上文有提到在 Vue 中计算属性对比函数执行：会有缓存，减少计算，因为计算属性只有在它的相关依赖发生改变时才会重新求值。

这就意味着只要  firstName 和 lastName 还没有发生改变，多次访问 fullName 计算属性会立即返回之前的计算结果，而不必再次执行函数。

那么是否 React 的 getter 也有缓存这个优势？？？  **答案是：没有，react 中的 getter 并没有做缓存优化**！

不过我们可以使用记忆化技术（memoization）来优化我们的计算属性，实现和 Vue 中计算属性一样的效果，我们需要在项目中引入 memoize-one 库，实现代码如下：

```js
import memoize from 'memoize-one';
import React, { Fragment, Component } from 'react';

class Example extends Component {
  state = {
    firstName: '',
    lastName: '',
  };

  // 如果和上次参数一样，`memoize-one` 会重复使用上一次的值。
  getFullName = memoize((firstName, lastName) => `${firstName} ${lastName}`);

  get fullName() {
    return this.getFullName(this.state.firstName, this.state.lastName);
  }

  render() {
    return <Fragment>{this.fullName}</Fragment>;
  }
}
```

### 使用 React hooks 优化计算属性

上文在 React 中使用了 memoize-one 库实现了类似 Vue 计算属性（computed）的效果 —— 基于依赖缓存计算结果。得益于React 16.8 新推出的Hooks特性，我们可以对逻辑进行更优雅的封装。

首先，我们来介绍一下`useMemo`：

官方对`useMemo`的介绍在[这里](https://zh-hans.reactjs.org/docs/hooks-reference.html#usememo)， 简而言之，就是我们传入一个**回调函数**和一个**依赖列表**，React会在依赖列表中的值变化时，调用这个回调函数，并将回调函数返回的结果进行缓存。

```js
import React, { useState, useMemo } from 'react';

function Example(props) {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  // 使用 useMemo 函数缓存计算过程
  const renderFullName = useMemo(() => `${firstName} ${lastName}`, [
    firstName,
    lastName,
  ]);

  return <div>{renderFullName}</div>;
}
```

### 总结

本文介绍了在 React 中如何实现计算属性，在 React 中实现了类似 Vue 计算属性（computed）的效果 —— 基于依赖缓存计算结果，实现逻辑计算与视图渲染的解耦，降低 render 函数的复杂度。

从业务开发角度来讲，Vue 提供的 API 极大地提高了开发效率。

React 官方虽然某些场景没有原生的 API 支持，但得益于活跃的社区，工作中遇到的问题总能找到解决方案，并且在摸索这些解决方案的同时，我们能够学习到诸多经典的编程思想，从而更加合理的运用框架、技术解决业务问题。
