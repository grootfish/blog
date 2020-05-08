---
title: hooks 已经到来
tags: [javascript,react]
description: react 知识整理
---
## Hooks 已经到来

### 前言

Hooks 是 React 16.8 新增的特性，它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。
Redux 的作者 Dan Abramov 在 2018 年的 ReactConf 向大家首次介绍了 React Hooks。React Hooks 是为了解决 Class Component 的一些问题而引入的。

Vue 的作者尤雨溪在 VueConf China 2019 也给 Vue 3.0 引入了一个叫 Functional-based API 的概念，它是受 React Hooks 启发而增加的新 API。由于 Vue 2.0 组件组合的模式是对象字面量形式，所以 Functional-based API 可以作为 Mixins 的替代，配合新的响应式 API 作为新的组件组合模式。React 和 Vue 对于降低前端开发复杂度这一问题都不约而同地选择了 Hooks 这一方案，这到底是为什么呢？

### 为什么要用 Hooks 

React Hooks 是为了解决 Class Component 的一些问题而引入的

- 类组件间的逻辑难以复用。因为 JavaScript 不像 Go 或 C++ 一样，Class 可以多重继承，类的逻辑的复用就成了一个问题；
- 类组件经常会在生命周期做一些数据获取事件监听的副作用函数，这样的情况下我们就很难把组件拆分为更小的力度；
- 类组件 this 指向问题令人迷惑。很多新手应该会被 Class 组件绑定事件的 this 迷惑过，绑定事件可以用 bind，可以直接写箭头函数，也可以写类属性函数，但到底哪种方法才是最好的呢？

Hooks 可以使我们模块化开发的粒度更细，更函数式。组件的功能变成了由 Hooks 一点点地装配起来。这样的特性，也解决了上面提到的痛点：代码复用、组件树过深、类组件问题。

对于代码逻辑复用，React 和 Vue 都提出过各自的解决方案，其中比较常用的逻辑复用方案是 `mixins`、`HOC`。这两种方案都可以实现逻辑上的复用，但是都有一些额外的问题:

- mixins 的问题：
  - 首先是命名空间耦合，如果多个对象同名参数，这些参数就会耦合在一起;
  - 由于 mixins 必须是运行时才能知道具体有什么参数，所以是 TypeScript 是无法做静态检查的
  - 组件参数不清晰，在 mixins 中组件的 props 和其他参数没什么两样，很容易被其它的 mixins 覆盖掉
- HOC 高阶组件的问题：
  - 需要在原组件上进行包裹或者嵌套，如果大量使用HOC，将会产生非常多的嵌套，让调试变得困难；
  - 每多用一次高阶组件，都会多出一个组件实例
  - 在不遵守约定的情况下 props 还是有可能会在高阶组件中被更改

Hooks 的出现很好的解决了上面的问题。因为 Hooks 跑在一个普通函数式组件里，所以他没有命名空间的问题，同时 TypeScript 也能对普通函数做很好的静态检查，而且 Hooks 也不能更改组件的 Props，传入的是啥最后可用的就是啥；最后 Hooks 基于函数式编程，不会产生多个组件实例。

### Vue Hooks 与 React Hooks 的差异

#### 简单语法的差异

```javascript
// React hooks 的基本使用

function Demo() {
  // 初始化 firstName 状态变量
  const [firstName, setFirstName] = useState('Billie');

  // 持久化 name 数据的副作用
  useEffect(function persist() {
    if(name.value !== '') {
      localStorage.setItem('nameData', name);
    }
  });
  
  // 初始化 lastName 状态变量
  const [lastName, setLastName] = useState('Eilish');

  // 更新 title 的副作用
  useEffect(function updateTitle() {
    document.title = `${firstName} ${lastName}`;
  });

  // ...
}
```

```javascript
// Vue hooks 的基本使用
import {ref, watch} from 'Vue';
export default {
  setup() {
    // 使用 firstName 状态变量
    const firstName = ref("Billie");
    // 使用一个 watcher 以持久化数据
    if(name.value !== '') {
      watch(function persist() => {
        localStorage.setItem('nameData', name.value);
      });
    }
   // 使用 lastName 状态变量
   const lastName = ref("Eilish");
   // 使用一个 watcher 以更新 title
   watch(function updateTitle() {
     document.title = `${firstName.value} ${lastName.value}`;
   });
  }
}

```

Vue 中的 setup 仅执行一遍，而 React Function Component 每次渲染都会执行。

对 React Hooks 而言，调用必须放在最前面，而且不能被包含在条件语句里，这是因为 React Hooks 采用下标方式寻找状态，一旦位置不对或者 Hooks 放在了条件中，就无法正确找到对应位置的值。

而 Vue Function API 中的 Hooks 可以放在任意位置、任意命名、被条件语句任意包裹的，因为其并不会触发 setup 的更新，只在需要的时候更新自己的引用值即可，利用 Proxy 监听机制，可以做到 setup 函数不重新执行，但 Template 重新渲染的效果


#### 自定义 hooks 的差异

```javascript
// React 中使用 hooks 
import React, { useState, useEffect } from 'react';

// 自定义hook useMouse
const useMouse = () => {
    // 使用hookuseState初始化一个state
    const [postion, setPostion] = useState({ x: 0, y: 0 });
    function handleMove(e) {
        setPostion({ x: e.clientX, y: e.clientY });
    }

    useEffect(() => {
        window.addEventListener('mousemove', handleMove);
        return () => {
            window.removeEventListener('mousemove', handleMove);
        };
    }, [postion]);
    return postion;
};

export default function App() {
    const { x, y } = useMouse(); // 内部维护自己的postion相关的逻辑
    return (
        <div>
            current position x: {x}, y: {y}
        </div>
    );
}
```

```javascript
// Vue 中使用 hooks
import { value, computed, watch, onMounted } from 'vue'

function useMouse() {
  const x = value(0)
  const y = value(0)
  const update = e => {
    x.value = e.pageX
    y.value = e.pageY
  }
  onMounted(() => {
    window.addEventListener('mousemove', update)
  })
  onUnmounted(() => {
    window.removeEventListener('mousemove', update)
  })
  return { x, y }
}

// 在组件中使用该函数
const Component = {
  setup() {
    const { x, y } = useMouse()
  
    return { x, y }
  },
  template: `<div>{{ x }} {{ y }} </div>`
}
```

通过上面的代码我们可以看到很多使用上的相同之处，都是把可以复用的一些单独的逻辑抽离到一个单独的函数中去，同时返回组件中需要用到的数据，并且内部会自我维护数据的更新，从而触发视图的更新。

不过在 React 中，useMouse 如果修改了 x 的值，那么使用 useMouse 的函数就会被重新执行，以此拿到最新的 x，而在 Vue 中利用 Proxy 监听机制，可以做到 setup 函数不重新执行，但 Template 重新渲染的效果。

### 总结

Hooks 的应用前景还是挺好的，解决了目前前端开发中的诸多痛点。不过 React Hooks 生态还不够完善，而 Vue 只有个 [Hooks POC](https://github.com/yyx990803/vue-hooks)，Vue3.0 很可能会加上，但需要再等等。
本篇文章着重解释一下我对 Hooks 的理解，以及 Hooks API 在 Vue 和 React 框架中的不同。也说明一下 Hooks 是个中立的概念，可以在任何框架中使用。
