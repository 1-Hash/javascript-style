# airbnb React/JSX 风格指南
*一个 React 和 JSX 比较普遍的规范指南*
## 目录

  1. [Basic Rules](#基础的规则)
  1. [Class vs `React.createClass` vs stateless](#class-vs-reactcreateclass-vs-stateless)
  1. [Mixins](#mixins)
  1. [Naming](#命名)
  1. [Declaration](#声明)
  1. [Alignment](#对齐)
  1. [Quotes](#quotes)
  1. [Spacing](#空格)
  1. [Props](#属性)
  1. [Refs](#refs)
  1. [Parentheses](#括号)
  1. [Tags](#标签)
  1. [Methods](#方法)
  1. [Ordering](#生命周期)
  1. [`isMounted`](#ismounted)

## 基础的规则

  - 每个文件中只能包含一个React组件

    - 然而，多个无状态组件(https://facebook.github.io/react/docs/reusable-components.html#stateless-functions)是允许在同一个文件中的。eslint: [`react/no-multi-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless).

  - 总是使用JSX语法

  - 除非你正在从一个不是JSX的文件中初始化一个app，否则不要使用`React.createElement`

## Class vs `React.createClass` vs stateless

  - 如果你有内部的state和/或者refs，最好使用`class extends React.Component`来代替`React.createClass`，eslint: [`react/prefer-es6-class`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md) [`react/prefer-stateless-function`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)

    ```jsx的写法
    // bad
    const Listing = React.createClass({
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    });

    // good
    class Listing extends React.Component {
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    }
    ```

    And if you don't have state or refs, prefer normal functions (not arrow functions) over classes:
    如果你没有state或者refs，推荐使用普通函数（不是箭头函数）而不是类
    ```jsx
    // bad
    class Listing extends React.Component {
      render() {
        return <div>{this.props.hello}</div>;
      }
    }

    // bad (relying on function name inference is discouraged)(依赖函数名)
    const Listing = ({ hello }) => (
      <div>{hello}</div>
    );

    // good
    function Listing({ hello }) {
      return <div>{hello}</div>;
    }
    ```

## Mixins

  - [不要使用mixins](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html)。

  > 为什么?Mixins会增加隐式的依赖关系，造成命名冲突，也会滚雪球式的增加复杂性。大多数情况下使用mixins能通过组件，高阶组件，或者工具模块更好的完成。

## 命名

  - **后缀、拓展名**：React组件使用 `.jsx`后缀

  - **文件名**:文件名使用帕斯卡拼写法，eg：`ReservationCard.jsx`。（帕斯卡拼写法,所有单词的首字母大写，然后直接连接起来，单词之间没有连接符）

  - **引用命名**:React组件使用帕斯卡拼写法，实例使用驼峰拼写法。 eslint: [`react/jsx-pascal-case`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-pascal-case.md)

    ```jsx
    // bad
    import reservationCard from './ReservationCard';

    // good
    import ReservationCard from './ReservationCard';

    // bad
    const ReservationItem = <ReservationCard />;

    // good
    const reservationItem = <ReservationCard />;
    ```

  - **组件命名**:使用文件名作为组件名,比如，`ReservationCard.jsx`应该有一个命名为`ReservationCard`的组件名，然而，对于一个文件夹的根组件，使用`index.jsx`作为入口文件，然后使用index.js或者文件夹名作为模块的名称.
    ```jsx
    // bad
    import Footer from './Footer/Footer';

    // bad
    import Footer from './Footer/index';

    // good
    import Footer from './Footer';
    ```
  - **高阶组件命名**:在生成的组件中使用高阶组件名和传入组件名作为`displayName`的组合，比如，高阶组件`withFoo()`，当传入一个组件`Bar`时就产生一个带有`displayName`的组件`withFoo(Bar)`。

  > 为什么？一个组件的`displayName`可能会被开发者工具所使用或者会提示错误信息，有一个能够清楚地表达了这个关系的值帮助人们理解组件发生了什么
    ```jsx
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

  - **属性命名**:避免为了不同的目的使用DOM组件属性名

  > 为什么？人们希望像`style` 和 `className`的属性代表一些特殊的含义，在你的APP中使用这些属性代表其他的含义会使得你的代码更难阅读，也更难维护，并且可能造成bug。
    ```jsx
    // bad
    <MyComponent style="fancy" />

    // good
    <MyComponent variant="fancy" />
    ```


## 声明 

  - 不要使用`displayName`为组件命名，相反的，通过引用给组件命名
    ```jsx
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

  - 下面的这些是JSX语法的对齐风格。eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)
    ```jsx
    // bad
    <Foo superLongParam="bar"
         anotherSuperLongParam="baz" />

    // good
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    />


    // 如果可以属性在一行那就在同一行吧，
    <Foo bar="bar" />

    // 子属性按照常规方式缩进
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    >
      <Quux />
    </Foo>
    ```


## 引号

  - JSX属性最好使用双引号，但是单引号适用于所有的JS。eslint: [`jsx-quotes`](http://eslint.org/docs/rules/jsx-quotes)

  > 为什么？合格的HTML属性也通常使用双引号而不是单引号，所以JSX属性也遵循这个惯例。
    ```jsx
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

  - 通常在你的闭合标签前包含一个空格，eslint: [`no-multi-spaces`](http://eslint.org/docs/rules/no-multi-spaces), [`react/jsx-space-before-closing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-space-before-closing.md)
    ```jsx
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

  - 不在花括号{}两边加空格。eslint: [`react/jsx-curly-spacing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)
    ```jsx
    // bad
    <Foo bar={ baz } />

    // good
    <Foo bar={baz} />
    ```

## 属性
  - 常常使用驼峰命名法为属性命名

    ```jsx
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

  - 当属性很明确的为`true`时省略这个值。eslint: [`react/jsx-boolean-value`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)
    ```jsx
    // bad
    <Foo
      hidden={true}
    />

    // good
    <Foo
      hidden
    />
    ```


  - 常常在`<img>`标签中包含一个`alt`属性,如果图片是可以显示的，`alt`可以是一个空字符串或者`<img>`必须有一个`role="presentation"`。eslint: [`jsx-a11y/img-has-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-has-alt.md)

    ```jsx
    // bad
    <img src="hello.jpg" />

    // good
    <img src="hello.jpg" alt="Me waving hello" />

    // good
    <img src="hello.jpg" alt="" />

    // good
    <img src="hello.jpg" role="presentation" />
    ```

  - 不要使用类似"image", "photo", 或者 "picture"的字眼在`<img>` 的`alt` 属性中。eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

  > 为什么？屏幕阅读器已经把`img`元素作为图片了，因此不需要在alt属性的文字中包含这些信息。
    ```jsx
    // bad
    <img src="hello.jpg" alt="Picture of me waving hello" />

    // good
    <img src="hello.jpg" alt="Me waving hello" />
    ```

  - 只使用有效的，非抽象的ARIA roles。(https://www.w3.org/TR/wai-aria/roles#role_definitions). eslint: [`jsx-a11y/aria-role`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)
    ```jsx
    // bad - not an ARIA role
    <div role="datepicker" />

    // bad - abstract ARIA role
    <div role="range" />

    // good
    <div role="button" />
    ```

  - 不要在标签元素中使用`accessKey`。eslint: [`jsx-a11y/no-access-key`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

  > 为什么？屏幕阅读器在键盘快捷键和键盘命令时造成的不统一会导致阅读性更加复杂
  ```jsx
  // bad
  <div accessKey="h" />

  // good
  <div />
  ```

  - 避免使用数组索引作为`key`的属性,最好用一个唯一的ID。([为啥？](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318))
  ```jsx
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

  - 为所有非必须的属性定义明确的defaultProps

  > 为什么？propTypes可以作为模块的文档说明，能够声明propTypes意味着你代码的读者不必去假设一些默认值，另外，也意味着你的代码能够跳过属性类型的检查。
  ```jsx
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
  function SFC({ foo, bar }) {
    return <div>{foo}{bar}</div>;
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


## Refs

  - 使用ref回调。eslint: [`react/no-string-refs`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-string-refs.md)
    ```jsx
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

  - 当JSX标签跨度超过一行那就用括号换行。eslint: [`react/jsx-wrap-multilines`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-wrap-multilines.md)
    ```jsx
    // bad
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

  - 单闭合标签是没有子元素的。eslint: [`react/self-closing-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)
    ```jsx
    // bad
    <Foo className="stuff"></Foo>

    // good
    <Foo className="stuff" />
    ```

  - 如果你的组件属性有多行，那么闭合标签应该另起一行。eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)
    ```jsx
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

## 方法 函数

  - 使用箭头函数去返回局部变量
    ```jsx
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

  - 在构造函数中为render方法绑定事件处理程序。eslint: [`react/jsx-no-bind`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

  > 为什么？在每次render过程中，再调用bind都会新建一个新的函数。
    ```jsx
    // bad
    class extends React.Component {
      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv.bind(this)} />
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
        return <div onClick={this.onClickDiv} />
      }
    }
    ```

 
  - 不要为一个React组件的内部方法使用下划线前缀

  > 为什么？下划线有时候是作为其他语言表示私有变量或者函数的一种惯例，但是，不像其他语言，在JavaScript中没有原生为私有提供支持，所有的变量函数都是公共的。

  > 尽管你的意思是使它私有化，在之前加上下划线并不会使这些变量私有化，并且所有属性，都应该视为是共有的。
  [#1024](https://github.com/airbnb/javascript/issues/1024), and [#490](https://github.com/airbnb/javascript/issues/490) for a more in-depth discussion.
    ```jsx
    // bad
    React.createClass({
      _onClickSubmit() {
        // do stuff
      },

      // other stuff
    });

    // good
    class extends React.Component {
      onClickSubmit() {
        // do stuff
      }

      // other stuff
    }
    ```

  - 在你的`render`方法中确保有return值。eslint: [`react/require-render-return`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/require-render-return.md)
    ```jsx
    // bad
    render() {
      (<div />);
    }

    // good
    render() {
      return (<div />);
    }
    ```

## Ordering
## 生命周期
  - `class extends React.Component`的生命周期:
  1. 可选的`static`方法
  1. `constructor`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *单击处理程序或者事件处理程序* `onClickSubmit()` 或 `onChangeDescription()`
  1. *`render`的getter方法*`getSelectReason()` 或 `getFooterContent()`
  1. *可选的render方法*`renderNavigation()` 或 `renderProfilePicture()`
  1. `render`

  - 怎样去定义`propTypes`, `defaultProps`, `contextTypes`,等
    ```jsx
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

  - `React.createClass`的生命周期: eslint: [`react/sort-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)
 
  1. `displayName` 设置模块名称
  1. `propTypes` 设置属性类型
  1. `contextTypes` 设置上下文类型
  1. `childContextTypes` 设置元素上下文类型
  1. `mixins` 添加一些mixins
  1. `statics`
  1. `defaultProps` 设置默认的属性值
  1. `getDefaultProps` 获取默认的属性值
  1. `getInitialState` 
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1.  *单机处理程序或者事件处理程序*比如`onClickSubmit()` 或 `onChangeDescription()`
  1.  *`render`的getter方法*比如 `getSelectReason()` 或 `getFooterContent()`
  1.  *可选的render方法*比如`renderNavigation()` 或 `renderProfilePicture()`
  1. `render`

## `isMounted`

  - 不要使用`isMounted`
  > 为什么？[`isMounted`是一个反设计模式的方法]，当使用ES6类的时候不是有效的，也将要被弃用。
  
  [anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html

## Translation
## 翻译、转化
  This JSX/React style guide is also available in other languages:
  下面的JSX/React风格指南在其他语言也是有效的
  - ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese (Simplified)**: [JasonBoy/javascript](https://github.com/JasonBoy/javascript/tree/master/react)
  - ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [pietraszekl/javascript](https://github.com/pietraszekl/javascript/tree/master/react)
  - ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [apple77y/javascript](https://github.com/apple77y/javascript/tree/master/react)
  - ![Br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Portuguese**: [ronal2do/javascript](https://github.com/ronal2do/airbnb-react-styleguide)
  - ![jp](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/javascript-style-guide](https://github.com/mitsuruog/javascript-style-guide/tree/master/react)
  - ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Español**: [agrcrobles/javascript](https://github.com/agrcrobles/javascript/tree/master/react)
  - ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [ivanzusko/javascript](https://github.com/ivanzusko/javascript/tree/master/react)

**[⬆ back to top](#table-of-contents)**
