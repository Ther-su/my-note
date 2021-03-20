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

