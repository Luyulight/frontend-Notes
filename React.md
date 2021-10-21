### 1.react中的事件绑定问题

class写法需要在construct里对事件处理function进行bind(this)

原因是

```react
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 为了在回调中使用 `this`，这个绑定是必不可少的
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

render里jsx写法默认会调用React.createElement(里传入button的element)此时的this的执行上下文不再是class Toggle

有两种避免写法

1，使用实验性的class fileds写法

```react
class LoggingButton extends React.Component {
  // 此语法确保 `handleClick` 内的 `this` 已被绑定。
  // 注意: 这是 *实验性* 语法。
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

2,回调中使用箭头函数

```react
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // 此语法确保 `handleClick` 内的 `this` 已被绑定。
    return (
      <button onClick={() => this.handleClick()}>
        Click me
      </button>
    );
  }
}
```

此语法问题在于每次渲染 `LoggingButton` 时都会创建不同的回调函数。在大多数情况下，这没什么问题，但如果该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。我们通常建议在构造器中绑定或使用 class fields 语法来避免这类性能问题。



## 2.react中的条件渲染

在render中判断元素变量

在render中使用&&来对元素按需加载

在子组件中判断prop来决定return是不是null

```

```

## 3.react函数式组件中使用stateHook，子组件调用包含setState的function修改state但是并没有触发子组件的变化

```js
 const [categories, setCategories] = useState(tempData.categories)
 const deleteCategroy = index => {
    let temp = categories
    temp.splice(index,1)
    setCategories(temp)
  }
```

错误在于let temp=categories实际上只是换了个指针，指向的内存中是同一个state的内存区域，并且直接修改了state变量。

因为传入setState方法中的temp和state本身的categories变量是同一个，

所以setState判定没有发生修改，所以不会发生后续的子组件的刷新

正确的写法是

```ts
const deleteCategroy = index => {
    let temp = [...categories]
    temp.splice(index, 1)
    setCategories(temp)
  }
```

## 4.父组件中想获取子组件的state

父组件：

```tsx
const Father = ()=>{
	const objectRef = useRef<JSX.Element>()
    const {yourfiledName}:any = objectRef.current
    .......
    return (
    	...
        <Child {...props} ref={objectRef} />
        ...
    )
}

```

子组件原来

```tsx
const Child:React.FC<ChildProps> = ({...})=>{
	...
	...
	...
	return (
		...
	)
} 
```

子组件修改后

```tsx
const Child = ({...}:ChildProps，ref:any) => {
	const [needUpState,setUpState] = useState()
	useImerativeHandle(ref, ()=>({
		yourfiledName:{
			needUpState
		}
	}))
	....
	....
	return (
		...
	)
}
export default forwardRef(Child)
```

但是这么写的话虽然编译能过能跑，但是会有好多type的编辑器报错。

需要把子组件的使用方式也改成<Child props={props} ref={ref} />

需要把原先挂在根元素上的属性集合成一个对线再挂在根元素上，

暂时不知道还有没有更好的解决办法。

## 5.UseContext来传递状态

创建context:

```typescript
import React from "react";

export const LoginContext = React.createContext({
  loginStatus: false,
  changeStatus: (loginRes:boolean,type:number)=>{}
})
```

Layout:

```tsx
import { LoginContext } from "./pages/Context";
function App() {
  const [loginStatus, setLoginStatus] = useState(getCookie('token').length>0)
  let state = {
    loginStatus: loginStatus,
    changeStatus: (loginRes:boolean,type:number)=>{
      setLoginStatus(type&&loginRes?true:false)
    }
  }
  return (
    <LoginContext.Provider value={state}>
      <BrowserRouter>
        <Switch>
          {routes.map(({ path, Comp }) => (
            <Route key={path} path={path}>
                {AuthRoute({path, loginStatus, Comp })}
            </Route>
          ))}
        </Switch>
      </BrowserRouter>
    </LoginContext.Provider>
  );
}
```

在compoent中更新context的状态

```tsx
export const Login = () => {
  const [username, setName] = useState('')
  const [password, setPwd] = useState('')
  const handleSubmit = () => {
    LoginHandler(username,password)
      .then((res)=>{
        if(res){
          context.changeStatus(true,1)
            ....
        }
      })
  }
  return (
  	...
  )
 }
```

## 6.用原生的方式实现dynamic import（延迟加载模块或者懒加载)

在ts项目下编写一个项目的router时

突然想到，能不能有以前写js项目的router的懒加载方式，用一个function来包裹component实现一个延迟加载，类似于

```js
const loginRoutes = [
    {
        path: '/login',
        exact: true,
        Component: () => (require('@/page/login').default),
        model: () => (require('@/page/login/models/login').default)
    },
]
```

当然了，如果在基于vite构建的react的项目下，这么写是会报错的

```
error:require is not defined
```

原因就是，require其实是在node环境下提供的一个加载模块的方式，在浏览器的引擎中并没有原生的require 的js方法，而浏览器支持ES6的import也是要在对应的模块以script标签的形式并且指定type为module才可以达成引入。

那么在require不能使用的情况下怎样才能原生实现一个延迟加载或者按需引入呢？

比如在根app下想延迟引入一个login component

```tsx
function App() {
    const [shouldRender, setShouldRender] = useState(false)
    //const [Component, setComponent] = useState<React.FC | null>(null)
    const compRef = useRef<React.FC>()
    useEffect(()=>{
        import("./pages/Login").then(({login})=>{
            //setComponent(Login)
            compRef.current = Login
            setShouldRender(true)
        },[])
    })
    return (
    	<div>
        	{compRef.current? (<compRef.current />):null}
        </div>
    )
}
```

为什么不用state的形式直接set，因为那么做跑不过编译，原因的话有待一个深入的了解。

总之，延迟加载大概的实现就是这么一个思路
