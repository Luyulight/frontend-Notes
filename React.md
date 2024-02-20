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



8

basicLayout 主题内容与一个鉴权相关的状态

9

promise没有办法取消

】

SWR库，或者blue.,..重新定义promise

## 10.state的更新原理

如示例

一个页面的layout中，一个groupState来管理左侧的菜单list

一个checkedGroupState来管理选中的菜单的数据

checkedGroupState的值是groupState数组的一个item。

当使用useEffect触发groupState刷新后，checkedGroupState的对象中对应的属性也会被刷新

## 11.modal的hack用法。

```tsx
export const AddUserModal:React.FC<{group:groupItem|undefined,callback:()=>void}> & {
  show?:()=>void
  hide?:()=>void
}=({group,callback})=>{
  const [modalShow, setmodalShow] = useState(false)
  const [value, setvalue] = useState([])
  const members = group?.members || []
  useEffect(()=>{
    AddUserModal.show = ()=>{
      setmodalShow(true)
    }
    AddUserModal.hide = ()=>{
      setmodalShow(false)
    }
  },[null])
```

仅适用于全局且唯一的模态框

在第一次挂载的时候给AddUserModal的原型挂上show和hide方法，而在其他任意地方使用到模态框仅需要AddUserModal.show()即可

## 12.在局部模块修改全局框架的样式

比如在一个业务modal中，需要对antd的Radio.Group组件的disable的样式进行修改。

如果在非模块化的普通业务中，我们只需要在当前页面的html/php/等的view文件中对样式进行覆写，比如：

```css
.ant-radio-button-wrapper.ant-radio-button-wrapper-checked.ant-radio-button-wrapper-disabled {
  background-color: #a3dbce !important;
}
```

但是这种写法如果用到模块化的工程项目中，

首先在less/sass/scss中我们写这样的样式，其实在打包过程中经过css-module的转化，每个.class都会被加上哈希串，比如

```css
._modal-body_96ap7_12 ._previewArea_96ap7_36 ._itemAuth_96ap7_40 {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding-left: 10px;
    padding-right: 10px;
    border-bottom: 1px solid #e1dddd;
    height: 2.5rem;
    line-height: 2.5rem;
}
```

所以一种想法就是把上述的css写到一个单独的css中，然后在对应的组件的tsx头部直接import进来

```tsx
import './additional.css'
```

但是这样的话实际上会造成全局的样式污染。

我们还是应该统一写到less/sass/scss中

但是要避免被加入哈希，所以去查了一下css-module的用法

**CSS Modules 允许使用`:global(.className)`的语法，声明一个全局规则。凡是这样声明的`class`，都不会被编译成哈希字符串。**

所以以上代码改成了

```less
:global(.ant-radio-button-wrapper.ant-radio-button-wrapper-checked.ant-radio-button-wrapper-disabled) {
  background-color: #a3dbce !important;
}
```

加入到less中，但是这样其实还是会造成全局的样式污染。

我们要做的是

```tsx
<Radio.Group
	className={styles.myRadioGroup}
/>
```

```less
.myRadioGroup {
    :global(.ant-radio-button-wrapper.ant-radio-button-wrapper-checked.ant-radio-button-wrapper-disabled) {
        background-color: #a3dbce;
    }
}
```

用一个自定义的className包裹我们要修改的全局框架的样式

这样既能达到自定义的效果又不会污染全局的css



## 13.做一个input删除的拦截，如果是{{x}}的data则整块删除

```react
const [value, setValue] = useState("{{abc}}{{def}}")

    const deleteFilter = (e: React.KeyboardEvent) => {
        const reg = /{{\w*}}/g
        const tailReg = /{{\w*}}*$/
        if (!tailReg.test(value)) {
            return
        }
        if (e.key === "Backspace" || e.code === "Backspace") {
            e.preventDefault()
            let tempArr = value.match(reg)
            if (!tempArr || tempArr.length == 0) {
                return
            }
            let tailStr = tempArr[tempArr.length - 1]
            let valueCop = value.substring(0, value.length - tailStr.length)
            setValue(valueCop)
        }
    }
```

```tsx
<input
    value={value}
    onChange={(e) => setValue(e.target.value)}
    onKeyDown={(e) => deleteFilter(e)}
></input>
```

## 14.多个组件的执行顺序

#### 1. 父子组件

- **挂载阶段**

  分 **两个** 阶段：

  - 第 **一** 阶段，由父组件开始执行到自身的 `render`，解析其下有哪些子组件需要渲染，并对其中 **同步的子组件** 进行创建，按 **递归顺序** 挨个执行各个子组件至 `render`，生成到父子组件对应的 Virtual DOM 树，并 commit 到 DOM。
  - 第 **二** 阶段，此时 DOM 节点已经生成完毕，组件挂载完成，开始后续流程。先依次触发同步子组件各自的 `componentDidMount`，最后触发父组件的。

**注意**：如果父组件中包含异步子组件，则会在父组件挂载完成后被创建。

所以执行顺序是：

父组件 getDerivedStateFromProps —> 同步子组件 getDerivedStateFromProps —> 同步子组件 componentDidMount —> 父组件 componentDidMount —> 异步子组件 getDerivedStateFromProps —> 异步子组件 componentDidMount

- **更新阶段**

  **React 的设计遵循单向数据流模型** ，也就是说，数据均是由父组件流向子组件。

  ```
  第 一 阶段，由父组件开始，执行
  static getDerivedStateFromProps
  shouldComponentUpdate
  更新到自身的 `render`，解析其下有哪些子组件需要渲染，并对 **子组件** 进行创建，按 **递归顺序** 挨个执行各个子组件至 `render`，生成到父子组件对应的 Virtual DOM 树，并与已有的 Virtual DOM 树 比较，计算出 **Virtual DOM 真正变化的部分** ，并只针对该部分进行的原生DOM操作。
  ```

  ```
  第 二 阶段，此时 DOM 节点已经生成完毕，组件挂载完成，开始后续流程。先依次触发同步子组件以下函数，最后触发父组件的。
  getSnapshotBeforeUpdate()
  componentDidUpdate()
  
  React 会按照上面的顺序依次执行这些函数，每个函数都是各个子组件的先执行，然后才是父组件的执行。
  所以执行顺序是：
  父组件 getDerivedStateFromProps —> 父组件 shouldComponentUpdate —> 子组件 getDerivedStateFromProps —> 子组件 shouldComponentUpdate —> 子组件 getSnapshotBeforeUpdate —>  父组件 getSnapshotBeforeUpdate —> 子组件 componentDidUpdate —> 父组件 componentDidUpdate
  ```

#### 2.兄弟组件

- 挂载阶段

  若是同步路由，它们的创建顺序和其在共同父组件中定义的先后顺序是 **一致** 的。

  若是异步路由，它们的创建顺序和 js 加载完成的顺序一致。

- 更新阶段、卸载阶段

  兄弟节点之间的通信主要是经过父组件（Redux 和 Context 也是通过改变父组件传递下来的 `props` 实现的），**满足React 的设计遵循单向数据流模型**， **因此任何两个组件之间的通信，本质上都可以归结为父子组件更新的情况** 。

  所以，兄弟组件更新、卸载阶段，请参考 **父子组件**。

## 15.事件处理过程中需要弹窗进行验证以进行后续操作

通常我们能想到的是直接在代码中调用Modal.confirm()

```jsx
if (index > -1) {} else {
    Modal.confirm({
        title: "操作确认",
        content: (
            <div>
                确定要把词条 [
                <a
                    href={`/term/${record.id}`}
                    target="_blank"
                >
                    {record.name}
                </a>)
                ] 移动到 [
                <a
                    href={`/term/${parent.id}`}
                    target="_blank"
                >
                    {parent.name}
                </a>
                ] 下吗?
            </div>
        ),
        onOk: run,
    })
}
```

但是这样写的话在事件处理代码中不够优雅

可以将调用Modal的部分代码封装到一个promise中

如下

```typescript
import { ReactNode } from "react"
import { Modal } from "antd"

export function conditionalConfirm(
    content: ReactNode | string,
    condition: () => boolean
) {
    return new Promise((resolve, reject) => {
        if (condition()) {
            Modal.confirm({
                content,
                onOk: resolve,
                onCancel: reject,
            })
        } else {
            resolve("continue")
        }
    })
}

conditionalConfirm("",()=>true).then(()=>{
    callback()
})
```

实际上这种写法是没有必要的

CSS chrome99 新特性 @layers



16.useEffect依赖一个对象的state

即使每次setState的都是相同元素值的对象，但实际上每次set的都是一个不同的对象，所以React会认为实际上一直在不停的刷新。

所以这种情况应该选择把对象拆开用基本的数据类型进行存储state



17.React需要在一个子节点中渲染外部ReactNode

使用ReactDom.createPortal(child,container)



18.React内部render使用anchor组件并且跟随外部容器的滚动进行锚点切换

在外层容器使用context,并且封装到hooks中，在目标层的组件内直接调用hooks





19.UseCallback时，不能只看callback调用了哪些变量来写依赖，

而应该分析什么时候触发了callback，触发的时候有没有其他在callback内调用其他function依赖的state发生变化
