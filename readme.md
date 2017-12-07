# React 编程规范(by [Airbnb](https://github.com/airbnb/javascript/tree/master/react))

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [基本规则](#%E5%9F%BA%E6%9C%AC%E8%A7%84%E5%88%99)
- [命名](#%E5%91%BD%E5%90%8D)
- [混淆](#%E6%B7%B7%E6%B7%86)
- [声明](#%E5%A3%B0%E6%98%8E)
- [对齐](#%E5%AF%B9%E9%BD%90)
- [Refs](#refs)
- [引号](#%E5%BC%95%E5%8F%B7)
- [空格](#%E7%A9%BA%E6%A0%BC)
- [属性](#%E5%B1%9E%E6%80%A7)
- [括号](#%E6%8B%AC%E5%8F%B7)
- [标签](#%E6%A0%87%E7%AD%BE)
- [方法](#%E6%96%B9%E6%B3%95)
- [顺序](#%E9%A1%BA%E5%BA%8F)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 基本规则

* 每个文件只包含一个 React 组件
  * 不过可以包含多个 [Stateless 或 Pure 组件](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions)。 eslint 规则：[react/no-multi-comp](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless)
* 使用 `JSX` 语法
* 除非是从一个非 `JSX` 文件中初始化 app，否则不要使用 `React.createElement`

**Class vs React.createClass**

* 如果需要管理内部状态或 `refs`，优先使用 `class extends React.Component`。eslint 规则：[react/prefer-es6-class](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md), [react/prefer-stateless-function](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)

```js
// bad
const Listing = React.createClass({
  render() {
    return <div />;
  }
});

// good
class Listing extends React.Component {
  render() {
    return <div />;
  }
}
```

反之, 则优先使用普通函数(不建议使用箭头函数)。

```js
// bad
class Listing extends React.Component {
  render() {
    return <div>{this.props.hello}</div>;
  }
}

// bad 
const Listing = ({ hello }) => (
  <div>{hello}</div>
);

// good
function Listing({ hello }) {
  return <div>{hello}</div>;
}
```

## 混淆
* [不要使用混淆](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html)

> 为什么? Mixins 会增加隐式的依赖，导致命名冲突，并且会以雪球式增加复杂度。在大多数情况下Mixins可以被更好的方法替代，如：组件化，高阶组件，工具模块等。

## 命名

* **扩展名:** 使用 `jsx` 作为 React 组件的扩展名
* **文件名:** 文件命名采用帕斯卡命名法，如：`ReservationCard.jsx`
* **引用名:**  组件引用采用帕斯卡命名法，其实例采用驼峰式命名法。eslint rules: [react/jsx-pascal-case](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-pascal-case.md)

```
// bad
const reservationCard = require('./ReservationCard');

// good
const ReservationCard = require('./ReservationCard');

// bad
const ReservationItem = <ReservationCard />;

// good
const reservationItem = <ReservationCard />;
```
* **组件命名:**  使用文件名作为组件名。例如：`ReservationCard.jsx` 组件的引用名应该是 `ReservationCard`。然而，对于一个目录的根组件，应该使用 `index.jsx` 作为文件名，使用目录名作为组件名。

```
// bad
const Footer = require('./Footer/Footer.jsx')

// bad
const Footer = require('./Footer/index.jsx')

// good
const Footer = require('./Footer')
```

* **高阶组件命名:** 如果是新生成的模块，其模块名的 `displayName` 应该为高阶模块名和传入模块名的组合. 例如, 高阶模块 `withFoo()`, 当传入一个 `Bar` 模块的时候， 新的模块名 `displayName` 应该为 withFoo(Bar).

> 为什么？一个模块的 `displayName` 可能会在开发者工具或者错误信息中使用到，因此有一个能清楚的表达这层关系的值能帮助我们更好的理解模块发生了什么，更好的Debug.

```js
// bad
export default function withFoo(WrappedComponent) {
  return function WithFoo(props) {
    return <WrappedComponent {...props} foo />;
  }
}

// good
export default function withFoo(WrappedComponent) {
  function WithFoo(props) {
    return <WrappedComponent {...props} foo />;
  }

  const wrappedComponentName = WrappedComponent.displayName
    || WrappedComponent.name
    || 'Component';

  WithFoo.displayName = `withFoo(${wrappedComponentName})`;
  return WithFoo;
}
```

* **属性命名:** 避免使用 DOM 属性为组件的属性命名.

> 为什么？对于 `style` 和 `className` 这样的属性名会默认代表一些含义，在你的应用中使用这些属性来表示其他的含义会使你的代码更难阅读，更难维护，并且可能会引起bug。

```js
// bad
<MyComponent style="fancy" />

// bad
<MyComponent className="fancy" />

// good
<MyComponent variant="fancy" />
```

## 声明

* 不要通过 `displayName` 来命名组件，通过引用来命名组件

```js
// bad
export default React.createClass({
  displayName: 'ReservationCard',
  // stuff goes here
});

// good
export default class ReservationCard extends React.Component {
}
```

## 对齐

* 对于 `JSX` 语法，遵循下面的对齐风格。eslint rules:  [react/jsx-closing-bracket-location](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)，
[react/jsx-closing-tag-location](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-tag-location.md)

```js
// bad
  <Foo superLongParam="bar"
       anotherSuperLongParam="baz" />

  // good
  <Foo
    superLongParam="bar"
    anotherSuperLongParam="baz"
  />

  // if props fit in one line then keep it on the same line
  <Foo bar="bar" />

  // children get indented normally
  <Foo
    superLongParam="bar"
    anotherSuperLongParam="baz"
  >
    <Spazz />
  </Foo>
```

## 引号

* 对于 `JSX` 使用双引号，对其它所有 JS 属性使用单引号。eslint：[jsx-quotes](https://eslint.org/docs/rules/jsx-quotes)

>为什么？因为 JSX 属性[不能包含被转移的引号](http://eslint.org/docs/rules/jsx-quotes)，并且双引号使得如 `"don't"` 一样的连接词很容易被输入。常规的 HTML 属性也应该使用双引号而不是单引号，JSX 属性反映了这个约定。

```js
 // bad
  <Foo bar='bar' />

  // good
  <Foo bar="bar" />

  // bad
  <Foo style={{ left: "20px" }} />

  // good
  <Foo style={{ left: '20px' }} />
```

## 空格

* 在自闭和标签之前留一个空格。eslint: [no-multi-spaces](https://eslint.org/docs/rules/no-multi-spaces), [react/jsx-tag-spacing](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-tag-spacing.md)

```js
// bad
<Foo/>

// very bad
<Foo                 />

// bad
<Foo
 />

// good
<Foo />
```

* 不要在JSX `{}` 引用括号里两边加空格. eslint: [react/jsx-curly-spacing](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)

```js
// bad
<Foo bar={ baz } />

// good
<Foo bar={baz} />
```

## 属性

* 属性名采用驼峰式命名法

```js
// bad
<Foo
  UserName="hello"
  phone_number={12345678}
/>

// good
<Foo
  userName="hello"
  phoneNumber={12345678}
/>
```

* 属性值为 `true`，可以忽略赋值。eslint: [react/jsx-boolean-value](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

```js
// bad
<Foo
  hidden={true}
/>

// good
<Foo
  hidden
/>

// good
<Foo hidden />
```

* `img` 标签要添加 `alt` 属性或者 `role="presentation"`。 eslint: [jsx-a11y/alt-text](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/alt-text.md)

```js
// bad
<img src="hello.jpg" />

// good
<img src="hello.jpg" alt="Me waving hello" />

// good
<img src="hello.jpg" alt="" />

// good
<img src="hello.jpg" role="presentation" />
```

* 不要在 `img` 标签的 `alt` 属性中使用 "image", "photo", 或 "picture" 一类的单词。eslint：[jsx-a11y/img-redundant-alt](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

> 为什么? 屏幕助读器已经将 img 作为图片了，所以没必要再在 alt 属性中进行说明

```js
// bad
<img src="hello.jpg" alt="Picture of me waving hello" />

// good
<img src="hello.jpg" alt="Me waving hello" />
```

* 只是用正确且具体的 [ARIA 属性](https://www.w3.org/TR/wai-aria/roles#role_definitions)。eslint：[jsx-a11y/aria-role](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

```js
// bad - not an ARIA role
<div role="datepicker" />

// bad - abstract ARIA role
<div role="range" />

// good
<div role="button" />
```

* 不要在元素上使用 `accessKey` 属性。eslint：[jsx-a11y/no-access-key](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

> 为什么？屏幕助读器在键盘快捷键与键盘命令时造成的不统一性会导致阅读性更加复杂。

```js
// bad
<div accessKey="h" />

// good
<div />
```

* 避免使用数组的 `index` 来作为属性 `key` 的值，推荐使用唯一ID([为什么?](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318))

```js
// bad
{todos.map((todo, index) =>
  <Todo
    {...todo}
    key={index}
  />
)}

// good
{todos.map(todo => (
  <Todo
    {...todo}
    key={todo.id}
  />
))}
```

* 对于组件所有的非必要属性需在 `defaultProps` 中定义。

> 为什么？propTypes 也是一种文档形式，提供 defaultProps 定义更有利于其他人阅读你的代码，并且能省略一些类型检查

```js
// bad
function SFC({ foo, bar, children }) {
  return <div>{foo}{bar}{children}</div>;
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};

// good
function SFC({ foo, bar, children }) {
  return <div>{foo}{bar}{children}</div>;
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};
SFC.defaultProps = {
  bar: '',
  children: null,
};
```

* 尽量少用扩展运算符

>为什么？除非你很想传递一些不必要的属性。对于React v15.6.1和更早的版本，你可以给[DOM传递一些无效的HTML属性](https://doc.react-china.org/blog/2017/09/08/dom-attributes-in-react-16.html)

例外情况：

  * 使用了变量提升的高阶组件
  ```js
  function HOC(WrappedComponent) {
    return class Proxy extends React.Component {
      Proxy.propTypes = {
        text: PropTypes.string,
        isLoading: PropTypes.bool
      };

      render() {
        return <WrappedComponent {...this.props} />
      }
    }
  }
  ```

  * 很清楚扩展运算符是用于对象时。在使用 Mocha 测试组件的时扩展运算符就非常有用
  ```js
  export default function Foo {
    const props = {
      text: '',
      isPublished: false
    }

    return (<div {...props} />);
  }    
  ```

  注意：使用时要尽可能过滤不必要的属性。使用 [prop-types-exact](https://www.npmjs.com/package/prop-types-exact)能预防 bug。

  ```js
  //good
  render() {
    const { irrelevantProp, ...relevantProps  } = this.props;
    return <WrappedComponent {...relevantProps} />
  }

  //bad
  render() {
    const { irrelevantProp, ...relevantProps  } = this.props;
    return <WrappedComponent {...this.props} />
  } 
  ```

## Refs

* 使用 ref callbacks。eslint：[react/no-string-refs](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-string-refs.md)

```js
// bad
<Foo
  ref="myRef"
/>

// good
<Foo
  ref={(ref) => { this.myRef = ref; }}
/>
```

## 括号

* 当组件跨行时，要用括号包裹 JSX 标签。eslint: [react/wrap-multilines](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/wrap-multilines.md)

```
/// bad
  render() {
    return <MyComponent className="long body" foo="bar">
             <MyChild />
           </MyComponent>;
  }

  // good
  render() {
    return (
      <MyComponent className="long body" foo="bar">
        <MyChild />
      </MyComponent>
    );
  }

  // good, when single line
  render() {
    const body = <div>hello</div>;
    return <MyComponent>{body}</MyComponent>;
  }
```

## 标签

* 没有子组件的父组件使用自闭和标签。eslint: [react/self-closing-comp](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)

```
// bad
  <Foo className="stuff"></Foo>

  // good
  <Foo className="stuff" />
```
* 如果组件有多行属性，闭合标签应写在新的一行上。eslint: [react/jsx-closing-bracket-location](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

```
// bad
  <Foo
    bar="bar"
    baz="baz" />

  // good
  <Foo
    bar="bar"
    baz="baz"
  />
```

## 方法

* 使用箭头函数来访问本地变量

```js
function ItemList(props) {
  return (
    <ul>
      {props.items.map((item, index) => (
        <Item
          key={item.key}
          onClick={() => doSomethingWith(item.name, index)}
        />
      ))}
    </ul>
  );
}
```

* 在构造函数中绑定需要在 `render` 方法使用的事件处理函数(绑定 `this`)。eslint：[react/jsx-no-bind](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

>为什么？在组件每次 `render` 时, 每次 bind 调用都会创建新的函数

```js
// bad
class extends React.Component {
  onClickDiv() {
    // do stuff
  }

  render() {
    return <div onClick={this.onClickDiv.bind(this)} />;
  }
}

// good
class extends React.Component {
  constructor(props) {
    super(props);

    this.onClickDiv = this.onClickDiv.bind(this);
  }

  onClickDiv() {
    // do stuff
  }

  render() {
    return <div onClick={this.onClickDiv} />;
  }
}
```

* 不要对 React 组件的内置方法使用 `underscore` 前缀

>为什么？`_` 前缀在某些语言中通常被用来表示私有变量或者函数，但在原生 JavaScript 不支持所谓的私有变量，所有的变量函数都是共有的。在变量名之前加上下划线并不会使这些变量私有化，并且所有的属性都应该被视为是共有的。相关 issue：[#1024](https://github.com/airbnb/javascript/issues/1024) / [#490](https://github.com/airbnb/javascript/issues/490) 。

```
// bad
React.createClass({
  _onClickSubmit() {
    // do stuff
  }

  // other stuff
});

// good
class extends React.Component {
  onClickSubmit() {
    // do stuff
  }

  // other stuff
});
```

* 确保 `render` 方法中有返回值。eslint: [react/require-render-return](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/require-render-return.md)

```js
// bad
render() {
  (<div />);
}

// good
render() {
  return (<div />);
}
```

## 顺序

* 继承 React.Component 的类的方法遵循下面的顺序

1. constructor
2. optional static methods
3. getChildContext
4. componentWillMount
5. componentDidMount
6. componentWillReceiveProps
7. shouldComponentUpdate
8. componentWillUpdate
9. componentDidUpdate
10. componentWillUnmount
11. clickHandlers or eventHandlers like onClickSubmit() or onChangeDescription()
12. getter methods for render like getSelectReason() or getFooterContent()
13. Optional render methods like renderNavigation() or renderProfilePicture()
14. render

* 怎么定义 propTypes，defaultProps，contextTypes 等等...

```
import React, { PropTypes } from 'react';

const propTypes = {
  id: PropTypes.number.isRequired,
  url: PropTypes.string.isRequired,
  text: PropTypes.string,
};

const defaultProps = {
  text: 'Hello World',
};

class Link extends React.Component {
  static methodsAreOk() {
    return true;
  }

  render() {
    return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>
  }
}

Link.propTypes = propTypes;
Link.defaultProps = defaultProps;

export default Link;
```

* 使用 React.createClass 时，方法顺序如下：

1. displayName
2. propTypes
3. contextTypes
4. childContextTypes
5. mixins
6. statics
7. defaultProps
8. getDefaultProps
9. getInitialState
10. getChildContext
11. componentWillMount
12. componentDidMount
13. componentWillReceiveProps
14. shouldComponentUpdate
15. componentWillUpdate
16. componentDidUpdate
17. componentWillUnmount
18. clickHandlers or eventHandlers like onClickSubmit() or onChangeDescription()
19. getter methods for render like getSelectReason() or getFooterContent()
20. Optional render methods like renderNavigation() or renderProfilePicture()
21. render

eslint: [react/sort-comp](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

## isMounted
* 不要再使用 `isMounted`. eslint: [react/no-is-mounted](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

> 为什么？[isMounted 是一种反模式](https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html)，在 ES6 classes 中不可用。官方未来将会删除改属性。·

### Related Resource
·
* [Vue Component Style Guide](https://github.com/pablohpsilva/vuejs-component-style-guide)
* [Node Style Guide](https://github.com/dwqs/node-style-guide)

