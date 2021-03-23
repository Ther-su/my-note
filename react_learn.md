---
typora-root-url: ./img
---

# 1、React 基础介绍

JS 实现简单 react

```js
const createDOMFromString = (str) => {
  const $div = document.createElement("div");
  $div.innerHTML = str;
  return $div;
};
class Component {
  constructor(props) {
    this.state = {};
    this.props = props || {};
  }
  setState = (state) => {
    this.state = state;
    const oldEle = this.el;
    this.el = this.renderDOM();
    this.updateUI && this.updateUI(oldEle, this.el);
  };
  _renderDOM = () => {
    this.el = createDOMFromString(this.render());
    this.onClick && this.el.addEventListener("click", this.onClick, false);
    return this.el;
  };
}
class LikeStar extends Component {
  constructor(props) {
    super(props);
    this.state = {
      isLiked: false,
    };
  }
  onClick = () => {
    this.setState({
      isLiked: false,
    });
  };
  render() {
    const { isLiked } = this.state;
    const { className } = this.props;
    const imgSrc = isLiked ? redStarSrc : whiteStarSrc;
    return `<img class="heart js-star ${className} src=${imgSrc}"`;
  }
}
const renderDOM = (instance, $parentNode) => {
  instance.updateUI = (oldEle, newEle) => {
    $parentNode.insertBefore(newEle, oldEle);
    $parentNode.removeChild(oldEle);
  };
  $parentNode.appendChild(instance._renderDOM());
};
renderDOM(new LikeStar(), $wrapper);
renderDOM(new LikeStar({ className: 'center-star' }), $centerWrapper);
```

同样内容用react实现

```js
class LikeStar extends React.Component{
  constructor(props) {
    super(props);
    this.state = {
      isLiked: false,
    };
  }
  onClick = () => {
    this.setState(prevState => {
      return {
        isLiked: !prevState.isLiked,
      }
    });
  }
  render() {
    const { isLiked } = this.state;
    const { className } = this.props;
    const imgSrc = isLiked ? redStarSrc : whiteStarSrc;
    return (
      <img className={`heart ${className}`} onClick={this.onClick} src={imgSrc} />
);
}
}
ReactDOM.render(
<LikeStar className="center-star"/>,
$centerWrapper,
);
ReactDOM.render(
<LikeStar />,
$wrapper,
);
```

# 2、JSX

JSX是一种用于描述UI的JS扩展语法，用于描述组件的UI，是React.createElement的语法糖

### 1、标签类型

DOM类型的标签 首字母必须小写

React组件类型的标签 首字母必须大写

### 2、注释

需要用大括号{}将/**/包裹起来

### 3、标签属性

1)DOM标签属性：id,className,style,onClick,onBlur

2)自定义标签属性

# 3、组件（props和state）

组件是React应用程序的基石

### 1、2种类型：

1）类组件

```js
class CommentList extends React.Component {
  render() {
    return (
    	<div>a</div>
    );
  }
}
```



2）函数组件

```js
function FunComponent(props) {
  return (
    	<div>a</div>
    );
}
```

### 2、组件的props

组件依赖的外部数据，由组件外部传入。唯一，不可变

利用库prop-types可对外部传入的属性值进行类型的校验

也可设置默认属性defaultProps

### 3、组件的state

表示组件内部状态，反映组件内部状态变化的数据

在constructor阶段被初始化

更新state 要利用setState 方法

- setState(stateChange[,callback])

  ```js
  this.setState({
    isLiked: !this.state.isLiked
  })
  ```

  

- setState(updater[,callback])

  ```js
  this.setState((prevState, props) => {
    return {
      isLiked: !prevState.isLiked
    }
  })
  ```

  

### 4、有状态组件和无状态组件

区别方式：组件内部是否有状态的变化

有状态组件：内部有状态的变化，需要state来反映变化

无状态组件：内部无状态的变化，不需要state

### 5、组件和元素

组件最核心的作用是返回React元素

class类型的组件 => render方法中return

function类型的组件 => return

# 4、组件生命周期

生命周期主要指类组件从创建到销毁的一系列过程。

函数组件内部没有状态，没有生命周期。

![image-20210320231923902](/image-20210320231923902.png)

## 1、挂载阶段

![image-20210320232321712](/image-20210320232321712.png)

### 1.1、constructor

应用场景：

1）state初始化

2）进行方法绑定

### 1.2、getDerivedStateFromProps

在每次渲染前都会被调用。

static getDerivedStateFromProps(props, state) => obj || null

目的是：让组件在props变化时更新state

### 1.3、render

描述希望在界面上看到的UI效果。render函数应该为纯函数

是class类中唯一必须要实现的方法。

返回的类型：

1）React元素

2）数组或fragments

3）Portals

4）字符串或数值类型

5）布尔类型

### 1.4、componentDidMount

在组件挂载后（插入DOM树中）立即调用。

使用场景：

1）依赖于DOM节点的初始化

2）通过网络请求获取数据

## 2、更新阶段

![image-20210320233845802](/image-20210320233845802.png)

### 2.1、getDerivedStateFromProps

在每次渲染前都会被调用。

static getDerivedStateFromProps(props, state) => obj || null

### 2.2、shouldComponentUpdate

决定组件是否继续执行更新过程

shouldComponentUpdate(nextProps,nextState)=>bool

作用：性能优化

### 2.3、render

在挂载和更新阶段都会被执行。更新阶段读取最新的props和state值，生成新的UI描述。

### 2.4、getSnapshotBeforeUpdate

在最近一次渲染输出（提交到DOM节点）之前调用。

getSnapshotBeforeUpdate(prevProps,prevState)=>snapshot || null

### 2.5、componentDidUpdate

在更新后会被立即调用

componentDidUpdate(prevProps,prevState,snapshot)=>void

应用：对更新后的DOM进行操作

## 3、卸载阶段

componentWillUnmount:在组件卸载及销毁之前直接调用。

应用场景：执行必要的清理操作

注意：不应调用setState()

## 4、其他

forceUpdate 强制组件更新

跳过shouldComponentUpdate方法，直接触发render方法的执行

# 5、事件处理

React中的事件是合成事件，不是原生事件

React事件中，必须显示地调用事件对象的preventDefault方法来阻止事件的默认行为。

e.nativeEvent可以调用原生事件

## 1、this处理

ES6中class不会为方法自动绑定this到当前对象

处理this的三种方式

### 1.1、箭头函数

多次render，多次创建不同的处理函数

<img src="/image-20210322010620627.png" alt="image-20210322010620627" style="zoom:67%;" />

### 1.2、函数绑定

多次render，不会创建不同的处理函数

<img src="/image-20210322010809615.png" alt="image-20210322010809615" style="zoom:67%;" />

### 1.3、class fields

<img src="/image-20210322010911347.png" alt="image-20210322010911347" style="zoom:67%;" />

## 2、参数传递

```jsx
<button onClick={(e)=>this.deleteRow(id,e)}>delete</button>
<button onClick={this.deleteRow.bind(this,id)}>delete</button>
```

# 6、列表

## 1、列表渲染

<img src="/image-20210322011250217.png" alt="image-20210322011250217" style="zoom:67%;" />

## 2、列表中key

React使用key属性来标记列表中的每个元素，当列表数据发生变化时，React就可以通过key知道哪些元素发生了变化，从而只重新渲染发生变化的元素，提高渲染效率。

元素的key需要放在就近的数组上下文中。

# 7、表单

## 1、受控组件

一个表单元素的值是由React来管理的，那么它就是一个受控组件。

<img src="/image-20210322092813637.png" alt="image-20210322092813637" style="zoom:50%;" />

## 2、非受控组件

非受控组件指表单元素的状态依然由表单元素自己管理，而不是交给React组件管理，实现要利用refs

### 2.1、refs

Refs是React中访问DOM节点、获取render方法中创建的React元素的一种方式。

### 2.2、refs使用方式

1）React.createRef

<img src="/image-20210322093429938.png" alt="image-20210322093429938" style="zoom:50%;" />

2）回调函数

<img src="/image-20210322093457168.png" alt="image-20210322093457168" style="zoom:50%;" />

### 2.3、refs使用场景

1）操作DOM节点

2）获取class类组件的实例

### 2.4、refs注意事项

1）函数组件不能使用refs

2）勿过度使用refs

# 8、context

context为组件间数据传递提供了新的方式。

主要应用场景：不同层级的组件需要访问同样一些的数据。

### 1、React.createContext

```jsx
const MyContext = React.createContext(defaultVaue)
```

### 2、Context.Provider

```jsx
<MyContext.Provider value={/* 某个值 */}></MyContext.Provider>
```

消费组件：被provider组件包裹，并且使用value值的组件。

### 3、class.contextType

<img src="/image-20210322101050225.png" alt="image-20210322101050225" style="zoom:50%;" />

在任何生命周期中访问。

### 4、Context.Consumer

```jsx
<MyContext.Consumer>
	{value=>/* 基于context值进行渲染 */}
</MyContext.Consumer>
```

函数作为子元素

### 5、注意事项

1）context的默认值只在外层无Provider组件包裹时被使用

2）Provider支持多层嵌套

3）消费组件的更新只取决于Provider值的更新，不受shouldComponentUpdate限制。

# 9、高阶组件

高阶组件（HOC）是React中用于复用组件逻辑的一种技巧。