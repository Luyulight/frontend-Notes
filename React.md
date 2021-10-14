1.react中的事件绑定问题

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



2.react中的条件渲染

在render中判断元素变量

在render中使用&&来对元素按需加载

在子组件中判断prop来决定return是不是null

```

```

3.react函数式组件中使用stateHook，子组件调用包含setState的function修改state但是并没有触发子组件的变化

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

