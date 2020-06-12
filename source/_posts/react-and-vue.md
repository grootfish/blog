---
title: React & Vue 对比
tags: [框架,React,vue]
# comments: true    // 是否开启评论
# date: 2018-12-10 15:28:14
description: 框架
---
## 编写语法
–	vue推荐的做法是webpack+vue-loader的单文件组件格式
–	react 使用 JSX 的语法
–	 vue 模板更贴近我们的HTML，可以让我们更直观地思考语义结构，更好地结合CSS的书写。JSX 使用 js 编写 DOM，代码可控性更强，更易调试
## 构建工具
–	vue-cli & create-react-app 前者提供多个可选模版，扩展性强。后者提供默认选项，复杂度低
## 数据绑定
–	Vue采用数据劫持&发布-订阅模式的方式，vue在创建vm的时候，会将数据配置在实例当中，然后通过Object.defineProperty对数据进行操作，为数据动态添加了getter与setter方法，当获取数据的时候会触发对应的getter方法，当设置数据的时候会触发对应的setter方法，从而进一步触发vm的watcher方法，然后数据更改，vm则会进一步触发视图更新操作。
–	react是单向数据流，组件实例状态只能通过setState来进行更改。调用setState更新this.state，它不是马上就会生效的，它是异步的。所以不要认为调用完setState后可以立马获取到最新的值。多个顺序执行的setState不是同步的一个接着一个的执行，会加入一个异步队列，然后最后一起执行，即批处理
## diff 算法
–	vue 中 diff 算法实现流程
  1.	在内存中构建虚拟 DOM
  2.	将内存中虚拟 DOM 树渲染成真是 DOM 结构
  3.	数据改变的时候，将之前的虚拟 DOM 树结合新的数据生成新的虚拟 DOM
  4.	将新的虚拟DOM 和上一次虚拟DOM 树进行对比（diff 算法进行比对），来更新只需要被替换的 DOM,而不是全部重绘。在Diff 算法中，只平层比较前后两颗DOM 树的节点，没有进行深度遍历
  5.	将对比出来的差异进行重新渲染
–	react 中 diff 算法实现流程
  1.	在内存中构建虚拟 DOM 描述页面元素
  2.	state 变更的时候，将之前的虚拟 DOM 结合新的数据生成新的虚拟DOM
  3.	DOM 结构发生改变 → 直接卸载并重新 create
  4.	DOM 结构一样 → 不卸载 ，update 更新的属性
  5.	所有同一层级的子节点.他们都可以通过key来区分-----同时遵循1.2两点（key的存在与否只会影响diff算法的复杂度）
  6.	diff 总共就是移动、删除、增加三个操作，而如果给每个节点唯一的标识（key），那么React优先采用移动的方式，能够找到正确的位置去插入新的节点。
–	vue会跟踪每一个组件的依赖关系，不需要重新渲染整个组件树。而对于React而言,每当应用的状态被改变时,全部组件都会重新渲染,所以react中会需要shouldComponentUpdate这个生命周期函数方法来进行控制。
## 性能优化
–	vue组件内部自动实现判断组件是否需要重新渲染 （defineObjectProptype）
–	react 当props或state发生改变的时候会触发,shouldComponentUpdate生命周期函数，优化 render 逻辑
–	react 引入 immutable 库配合 shouldComponentUpdate 优化
原生支持
–	react native & weex
## ssr 服务端渲染
–	Next.js （React）& Nuxt.js (Vue)
## 生命周期
–	Vue
–	初始化阶段 beforeCreate → created → beforeMount→mounted
–	更新阶段 beforeUpdated → updated
–	销毁阶段 beforeDestroy → destoryed
–	keep-alive 标签 active → deactive
–	React
–	初始化阶段 constructor → componentWillMount → shouldComponentUpdate → render → componentDidMount
–	更新阶段 getDerivedStateFromProps(componentWillReceiveProps) → shouldComponentUpdate → componentWillUpdate → render → componentDidUpdate
–	销毁阶段 componentWillUnmount
## 状态管理
–	vuex 的流程
1.	将需要共享的状态挂载到state上：this.$store.state来调用，vuex提供了mapState辅助函数，帮助我们在组件中获取并使用vuex的store中保存的状态。
2.	我们通过getters来创建状态：通过this.$store.getters来调用
3.	使用mutations来更改state：通过this.$store.commit来调用
4.	使用actions来处理异步操作：this.$store.dispatch来调用
–	rudux 的流程
1.	创建store： 从redux工具中取出createStore去生成一个store。
2.	创建一个reducer，然后将其传入到createStore中辅助store的创建。 reducer是一个纯函数，接收当前状态和action，返回一个状态，返回什么，store的状态就是什么，需要注意的是，不能直接操作当前状态，而是需要返回一个新的状态。 想要给store创建默认状态其实就是给reducer一个参数创建默认值。
3.	组件通过调用store.getState方法来使用store中的state，挂载在了自己的状态上。
4.	组件产生用户操作，调用actionCreator的方法创建一个action，利用store.dispatch方法传递给reducer
5.	reducer对action上的标示性信息做出判断后对新状态进行处理，然后返回新状态
6.	我们可以在组件中，利用store.subscribe方法去订阅数据的变化，也就是可以传入一个函数，当数据变化的时候，传入的函数会执行，在这个函数中让组件去获取最新的状态。

