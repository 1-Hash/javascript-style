# Airbnb CSS-in-JavaScript 样式指南
*一个 CSS-in-JavaScript 比较普遍的规范指南*
##目录
1. [命名](#命名)
1. [声明](#声明)
1. [嵌套](#嵌套)
1. [内联](#内联)
1. [主题](#主题)

#命名

  - 为对象的keys值使用驼峰命名（比如:"selectors"）.

  > 为什么要这样？我们在组件的 style 对象中通过这些keys值作为属性，所以使用驼峰命名法更方便.
    ```js
    // bad
    {
      'bermuda-triangle': {
        display: 'none',
      },
    }

    // good
    {
      bermudaTriangle: {
        display: 'none',
      },
    }
    ```

  - 为其他的样式修饰符使用下划线.

  > 为什么？类似于BEM，这个命名约定清楚地表明，样式旨在修改下划线前面的元素，下划线不需要被引用，所以使用相对于其他字符它是首选，比如破折号
    ```js
    // bad
    {
      bruceBanner: {
        color: 'pink',
        transition: 'color 10s',
      },

      bruceBannerTheHulk: {
        color: 'green',
      },
    }

    // good
    {
      bruceBanner: {
        color: 'pink',
        transition: 'color 10s',
      },

      bruceBanner_theHulk: {
        color: 'green',
      },
    }
    ```

  - 使用 `selectorName_fallback` 设置回退样式。

  > 为什么？类似于修饰符，保持命名一致有助于揭示这些样式与在更适当的浏览器中覆盖它们的样式之间的关系
    ```js
    // bad
    {
      muscles: {
        display: 'flex',
      },

      muscles_sadBears: {
        width: '100%',
      },
    }

    // good
    {
      muscles: {
        display: 'flex',
      },

      muscles_fallback: {
        width: '100%',
      },
    }
    ```

  - 使用一个单独的选择器设置回退样式。

  > 为什么？保留单独对象中包含的后被演示可以阐明其目的，这样提高可读性
    ```js
    // bad
    {
      muscles: {
        display: 'flex',
      },

      left: {
        flexGrow: 1,
        display: 'inline-block',
      },

      right: {
        display: 'inline-block',
      },
    }

    // good
    {
      muscles: {
        display: 'flex',
      },

      left: {
        flexGrow: 1,
      },

      left_fallback: {
        display: 'inline-block',
      },

      right_fallback: {
        display: 'inline-block',
      },
    }
    ```

  - 使用设备无关的名字（比如 "small", "medium", "large"）去命名媒体查询的断点

  > 为什么？通常来说使用像 “phone”，“tablet”和“desktop”这些名字不能在真实世界匹配设备的特点，使用这些名字设置错误的期望。
    ```js
    // bad
    const breakpoints = {
      mobile: '@media (max-width: 639px)',
      tablet: '@media (max-width: 1047px)',
      desktop: '@media (min-width: 1048px)',
    };

    // good
    const breakpoints = {
      small: '@media (max-width: 639px)',
      medium: '@media (max-width: 1047px)',
      large: '@media (min-width: 1048px)',
    };
    ``` 

## 声明

  - 在组件的后面定义样式

  > 为啥？我们使用高阶组件来主题话我们的样式，组件被定义之后很自然就可以被使用，通过style对象直接传递给此函数，这样减少了间接
    ```jsx
    // bad
    const styles = {
      container: {
        display: 'inline-block',
      },
    };

    function MyComponent({ styles }) {
      return (
        <div {...css(styles.container)}>
          Never doubt that a small group of thoughtful, committed citizens can
          change the world. Indeed, it’s the only thing that ever has.
        </div>
      );
    }

    export default withStyles(() => styles)(MyComponent);


    // good
    function MyComponent({ styles }) {
      return (
        <div {...css(styles.container)}>
          Never doubt that a small group of thoughtful, committed citizens can
          change the world. Indeed, it’s the only thing that ever has.
        </div>
      );
    }

    export default withStyles(() => ({
      container: {
        display: 'inline-block',
      },
    }))(MyComponent);
    ```

## 嵌套

  - 使用空白行在有相同缩进的相邻区块之间。

  > 为什么？空格提升了可读性同时减少了合并冲突的可能性
    ```js
    // bad
    {
      bigBang: {
        display: 'inline-block',
        '::before': {
          content: "''",
        },
      },
      universe: {
        border: 'none',
      },
    }

    // good
    {
      bigBang: {
        display: 'inline-block',

        '::before': {
          content: "''",
        },
      },

      universe: {
        border: 'none',
      },
    }
    ```

## 内联
 
  - 对具有高基数的样式使用内联样式（比如:使用一个属性的值，而不使用具有较低基数的样式

  > 为什么？生成主题样式表可能是昂贵的，因此最好是离散样式集
    ```jsx
    // bad
    export default function MyComponent({ spacing }) {
      return (
        <div style={{ display: 'table', margin: spacing }} />
      );
    }

    // good
    function MyComponent({ styles, spacing }) {
      return (
        <div {...css(styles.periodic, { margin: spacing })} />
      );
    }
    export default withStyles(() => ({
      periodic: {
        display: 'table',
      },
    }))(MyComponent);
    ```

## 主题

  - 使用一个抽象层类似于[react-width-styles](https://github.com/airbnb/react-with-styles)作为主题，*react-width-styles给我们提供了在一些文档中的例子中被使用的比如说`withStyles()`, `ThemedStyleSheet`, 和 `css()`*
 
  > 为什么？为你的组件设置一种共享的变量是很有用的。使用一个抽象层使得开发更方便，另外，它可以帮助防止您的组件紧密耦合到任何特定的底层实现，这能给你更多的自由。

  - 仅在主题中定义颜色
    ```js
    // bad
    export default withStyles(() => ({
      chuckNorris: {
        color: '#bada55',
      },
    }))(MyComponent);

    // good
    export default withStyles(({ color }) => ({
      chuckNorris: {
        color: color.badass,
      },
    }))(MyComponent);
    ```

  - 只在主题中定义字体
    ```js
    // bad
    export default withStyles(() => ({
      towerOfPisa: {
        fontStyle: 'italic',
      },
    }))(MyComponent);

    // good
    export default withStyles(({ font }) => ({
      towerOfPisa: {
        fontStyle: font.italic,
      },
    }))(MyComponent);
    ```

  - 将字体定义为相关样式集
    ```js
    // bad
    export default withStyles(() => ({
      towerOfPisa: {
        fontFamily: 'Italiana, "Times New Roman", serif',
        fontSize: '2em',
        fontStyle: 'italic',
        lineHeight: 1.5,
      },
    }))(MyComponent);

    // good
    export default withStyles(({ font }) => ({
      towerOfPisa: {
        ...font.italian,
      },
    }))(MyComponent);
    ```

  - 定义主题中的基本网格单位（作为值或者乘以乘数的函数）
    ```js
    // bad
    export default withStyles(() => ({
      rip: {
        bottom: '-6912px', // 6 feet
      },
    }))(MyComponent);

    // good
    export default withStyles(({ units }) => ({
      rip: {
        bottom: units(864), // 6 feet, assuming our unit is 8px
      },
    }))(MyComponent);

    // good
    export default withStyles(({ unit }) => ({
      rip: {
        bottom: 864 * unit, // 6 feet, assuming our unit is 8px
      },
    }))(MyComponent);
    ```

  - 只在主题中定义媒体查询
    ```js
    // bad
    export default withStyles(() => ({
      container: {
        width: '100%',

        '@media (max-width: 1047px)': {
          width: '50%',
        },
      },
    }))(MyComponent);

    // good
    export default withStyles(({ breakpoint }) => ({
      container: {
        width: '100%',

        [breakpoint.medium]: {
          width: '50%',
        },
      },
    }))(MyComponent);
    ```

  - 在主题中定义棘手的回调函数

  > 为什么？很多CSS-in-JavaScript实现将style对象合并在一起，这样就使得相同的属性（例如`display`）指定回调函数就有点棘手了，为了保持这些方法统一，就要把这些回调函数放在这个主题中。
 
    ```js
    // bad
    export default withStyles(() => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        'display ': 'table',
      },
    }))(MyComponent);

    // good
    export default withStyles(({ fallbacks }) => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        [fallbacks.display]: 'table',
      },
    }))(MyComponent);

    // good
    export default withStyles(({ fallback }) => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        [fallback('display')]: 'table',
      },
    }))(MyComponent);
    ```


  - 尽可能的减少创造自定义主题。很多APP都只有一个主题。

  - 在嵌套对象下的命名空间自定义主题设置具有唯一和描述性的键

    ```js
    // bad
    ThemedStyleSheet.registerTheme('mySection', {
      mySectionPrimaryColor: 'green',
    });

    // good
    ThemedStyleSheet.registerTheme('mySection', {
      mySection: {
        primaryColor: 'green',
      },
    });
    ```

---

CSS puns adapted from [Saijo George](http://saijogeorge.com/css-puns/).