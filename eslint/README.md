### eslint_airbnb
eslint作为前端js开发的代码检查工具，配置选择的是airbnb的基础配置, 和一些小改动。

### 在心理上接受它
[在多人协作的前端团队中，ESlint是非常有必要。同一项目组，互相代码review，多人协同开发同一需求等等，没有ESlint...](https://blog.csdn.net/gbwine/article/details/79180012)

### 使用
1.  在IDE中打开eslint工具
2.  配置根目录下的.eslintrc
3.  在package.json 中的"devDependencies" 字段中安装以下依赖：

         "babel-eslint": "^8.2.0",
         "eslint": "^4.15.0",
         "eslint-config-airbnb": "^16.1.0",
         "eslint-formatter-pretty": "^1.3.0",
         "eslint-import-resolver-webpack": "^0.8.4",
         "eslint-plugin-compat": "^2.1.0",
         "eslint-plugin-flowtype": "^2.41.0",
         "eslint-plugin-import": "^2.10.0",
         "eslint-plugin-jest": "^21.5.0",
         "eslint-plugin-jsx-a11y": "6.0.3",
         "eslint-plugin-node": "^6.0.1",
         "eslint-plugin-promise": "^3.7.0",
         "eslint-plugin-react": "^7.5.1"

### 资源
[Eslint官网](http://eslint.cn/docs/user-guide/configuring)

[airbnb/javascript](https://github.com/airbnb/javascript);

rules条目查询：

[javascript](http://eslint.cn/docs/rules)

[jsx-a11y](https://www.npmjs.com/package/eslint-plugin-jsx-a11y)

[eslint-plugin-react](https://www.npmjs.com/package/eslint-plugin-react)

[eslint-plugin-import](https://www.npmjs.com/package/eslint-plugin-import)

关于map生成的dom key值使用了index的问题：

[React之反模式index as key](https://segmentfault.com/a/1190000009149186)

产品化解决方案：

        npm install shortid -S
        var shortid = require('shortid');

eslint-import-resolver-webpack: 这个是告诉 eslint，使用 webpack 的 resolver 来检查 package 是否引用正确, 需要在项目中安装此模块
