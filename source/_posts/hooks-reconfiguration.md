---
title: 使React hooks 重构你的应用
tags: [React]
categories: [React]
description: 使React hooks 重构你的应用
---


### 什么是 Hooks

- React 一直都提倡使用函数组件，但是有时候需要使用 state 或者其他一些功能时，只能使用类组件，因为函数组件没有实例，没有生命周期函数，只有类组件才有
- Hooks 是 React 16.8 新增的特性，它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性
- 如果你在编写函数组件并意识到需要向其添加一些 state，以前的做法是必须将其它转化为 class。现在你可以直接在现有的函数组件中使用 Hooks

### 为什么要使用 Hooks

**1. 类组件的不足**    

- 状态逻辑难复用： 在组件之间复用状态逻辑很难，可能要用到 render props （渲染属性）或者 HOC（高阶组件），但无论是渲染属性，还是高阶组件，都会在原先的组件外包裹一层父容器（一般都是 div 元素），导致层级冗余
- 类组件中到处都是对状态的访问和处理，导致组件难以拆分成更小的组件
- this 指向问题：父组件给子组件传递函数时，必须绑定 this


```javascript
class App extends React.Component<any, any> {
    handleClick2;

    constructor(props) {
        super(props);
        this.state = {
            num: 1,
            title: ' react study'
        };
        this.handleClick2 = this.handleClick1.bind(this);
    }

    handleClick1() {
        this.setState({
            num: this.state.num + 1,
        })
    }

    handleClick3 = () => {
        this.setState({
            num: this.state.num + 1,
        })
    };

    render() {
        return (<div>
            <h2>Ann, {this.state.num}</h2>
            <button onClick={this.handleClick2}>btn1</button>
            <button onClick={this.handleClick1.bind(this)}>btn2</button>
            <button onClick={() => this.handleClick1()}>btn3</button>
            <button onClick={this.handleClick3}>btn4</button>
        </div>)
    }
}
```

- 以上代码是 react 中的组件四种绑定 this 方法的区别
  - 第一种是在构造函数中绑定 this：那么每次父组件刷新的时候，如果传递给子组件其他的 props 值不变，那么子组件就不会刷新；
  - 第二种是在 render() 函数里面绑定 this：因为 bind 函数会返回一个新的函数，所以每次父组件刷新时，都会重新生成一个函数，即使父组件传递给子组件其他的 props 值不变，子组件每次都会刷新；
  - 第三种是使用箭头函数：父组件刷新的时候，即使两个箭头函数的函数体是一样的，都会生成一个新的箭头函数，所以子组件每次都会刷新；
  - 第四种是使用类的静态属性：原理和第一种方法差不多，比第一种更简洁

*2. Hooks 优势*

- 能优化类组件的三大问题
- 能在无需修改组件结构的情况下复用状态逻辑（自定义 Hooks ）
- 能将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据）
- 副作用的关注点分离：副作用指那些没有发生在数据向视图转换过程中的逻辑，如 ajax 请求、访问原生dom 元素、本地持久化缓存、绑定/解绑事件、添加订阅、设置定时器、记录日志等。以往这些副作用都是写在类组件生命周期函数中的。

### 使用Hooks重构应用

1. 使用 initialState 函数处理状态初始值

```javascript
class MyComponent extends Component {
  constructor(props) {
    super(props)
    this.state = { token: null }
    if (this.props.token) {
      this.state.token = this.props.token
    } else {
      token = window.localStorage.getItem('app-token');
      if (token) {
        this.state.token = token
      }
    }
  }
}
```

```javascript
function MyComponent(props) {
   const initState = () => {
     if(props.token) {
       return props.token 
     } else {
       tokenLocal = window.localStorage.getItem('app-token');
       if (tokenLocal) {
         return tokenLocal
       }
     }
   };

   const [token, setToken] = useState(initState)
}
```

2. 使用 useEffect 代替生命周期 return 值清除 effect 操作

```javascript
function Counter(){
    let [number,setNumber] = useState(0);
    let [text,setText] = useState('');
    // 相当于componentDidMount 和 componentDidUpdate
    useEffect(()=>{
        console.log('开启一个新的定时器')
        let $timer = setInterval(()=>{
            setNumber(number=>number+1);
        },1000);
        // useEffect 如果返回一个函数的话，该函数会在组件卸载和更新时调用
        // useEffect 在执行副作用函数之前，会先调用上一次返回的函数
        // 如果要清除副作用，要么返回一个清除副作用的函数
       /*  return ()=>{
            console.log('destroy effect');
            clearInterval($timer);
        } */
    });
    // },[]);//要么在这里传入一个空的依赖项数组，这样就不会去重复执行
    return (
        <>
          <input value={text} onChange={(event)=>setText(event.target.value)}/>
          <p>{number}</p>
          <button>+</button>
        </>
    )
}

```

3. 使用 useReducer 简化代码

```javascript
const [state, dispatch] = useReducer(reducer)
const [state, dispatch] = useReducer((state, action) => newState)
```

如果你之前使用过redux ，那你就知道action 必须接收一个带有type属性的对象。然而，在useReducer 中，reducer 函数可以接收一个 state 以及多个 action ,然后返回一个新的state对象。

```javascript
function AppHooks() {
  const initialState = {
    data: null,
    error: null,
    loaded: false,
    fetching: false,
  }
  const [data,setData] = useState();
  ...
  const reducer = (state, newState) => ({ ...state, ...newState })
  const [state, setState] = useReducer(reducer, initialState);

  async function fetchData() {
    const response = await fetch(API_URL);
    const { data, status } = {
      data: await response.json(),
      status: response.status
    }

    if (status !== 200) {
      return setState({
        data,
        error: true,
        loaded: true,
        fetching: false,
      })
    }

    setState({
      data,
      error: null,
      loaded: true,
      fetching: false,
    })
  }

  useEffect(() => {
    fetchData()
  }, [])


  const { error, data } = state
  return error ? <div> Sorry, 网络异常 :( </div> :
    <pre>{JSON.stringify(data, null, ' ')}</pre>
}
```

4. 使用 useMemo 优化 useEffect中对象的比较

```javascript
function RandomNumberGenerator() {
  // look here 👇
  const name = {firstName: "name"}
	
  useEffect(() => {
    console.log("Effect has been run!")
  }, [name])

  const [randomNumber, setRandomNumber] = useState(0);    
  
  return <div>
    <h1> {randomNumber} </h1>
    <button onClick={() => { setRandomNumber(Math.random()) }}>Generate random number!</button>
  </div>
}
```

```javascript
function RandomNumberGenerator() {
  // look here 👇
  const name = useMemo(() => ({
    firstName: "name"
  }), [])
  
  useEffect(() => {
      console.log("Effect has been run!")
  }, [name])


  const [randomNumber, setRandomNumber] = useState(0)

  return <div>
    <h1>{randomNumber}</h1>
    <button onClick={() => { setRandomNumber(Math.random()) }}>
      Generate random number!</button>
  </div>
}

```
