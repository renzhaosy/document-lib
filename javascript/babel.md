# Babel

## 简介

Babel 是 JavaScript 编译器，更确切地说是源码到源码的编译器，通常也叫做“转换编译器（transpiler）。

## babel 配置

Babel 会在正在被转录的文件的当前目录中查找一个 `.babelrc` 文件。 如果不存在，它会遍历目录树，直到找到一个 `.babelrc` 文件，或一个 `package.json` 文件中有 `"babel": {}`

## 原理

Babel 的三个主要处理步骤： parse(解析)、transform(转换)、generate(生成)

### 编译过程

1. 核心包

```js
// 暴露babel.transform方法编译source  code
babel - core;

// 语法字符串解析 parse，解析成 `抽象语法树(AST)`
babylon;

// 结合plugins遍历AST语法树
babel - traverse;

// 生成最后编译字符串
babel - generator;
```

2. 编译流程

```js
input code string

-> babylon  parser
-> AST
-> babel-traverse //结合plugins遍历AST语法树,对AST节点进行添加、更新及移除等操作
-> AST
-> babel-generator
-> output string

```

3. 插件(plugins)和预设(presets)
   插件与预设的关系: 官方预设便是由官方评审维护的一系列插件组合。

```js
// .babelrc 配置
{
  "presets": ["react", "env"],
  "plugins": ["babel-plugin-me", "transform-runtime"]
}

// 编译顺序为
//  plugins: 从左往右
//  presets: 从右往左
// 起作用的顺序：babel-plugin-me -> transform-runtime -> env -> react

```

## 遍历

### Paths (路径)

AST 中有很多节点，节点和节点之间的联系用一个可操作和访问的`巨大可变对象`来表示，或者也可以用 Paths (路径)来简化

`Path` 表示两个节点之间连接的对象

## Scopes (作用域)

[演示](https://astexplorer.net/#/gist/61f470d64b0d156748c28b49783f7f68/c258f355e2d0c14d558afa52abf84adfdc2506ad)

```js
function a(n) {
  n * n;
}
let n = 1;
```

```js
visitor: {
  Identifier(path) {
    if (path.get("name").node === "n") {
      path.replaceWith(t.Identifier("_n"));
    }
  }
}

```

```js
visitor: {
 FunctionDeclaration(path) {
    const params = path.get("params");
    if (!params.length || !params[0]) return;
    const paramsName = params[0].get("name").node;
    path.traverse({
      Identifier(_path) {
        if (_path.get("name").node === paramsName) {
          _path.replaceWith(t.Identifier(`_${paramsName}`));
        }
      }
    });
  },
}

```

## API

### babylon

Babylon： babel 的解析器

### babel-traverse

Babel Traverse（遍历）模块维护了整棵树的状态，并且负责替换、移除和添加节点

### babel-types

Babel Types 模块是一个用于 AST 节点的 Lodash 式工具库， 它包含了构造、验证以及变换 AST 节点的方法。 该工具库包含考虑周到的工具方法，对编写处理 AST 逻辑非常有用

### babel-generator

Babel Generator 模块是 Babel 的代码生成器，它读取 AST 并将其转换为代码和源码映射（sourcemaps）

### babel-core

babel 转译器本身，提供了 babel 的转译 API，如 babel.transform 等，用于对代码进行转译

## 提示

本地完整测试时，可以在 `node_modules` 文件夹中创建 `babel-plugin-*` 的文件夹，然后 `index.js` 是入口文件，在 `.babelrc` 中配置

```
{
  "presets": ["react","env"],
  "plugins": ["babel-plugin-me", "transform-runtime"]
}

```

这样就可以直接使用了。

参考文档：

[babel 插件手册](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/zh-Hans/plugin-handbook.md)

[babel 文档](https://babeljs.io/docs/en/next/babel-core)

[ECMA](http://www.ecma-international.org/ecma-262/5.1/index.html#sec-8)

[AST explorer](https://astexplorer.net/#)
