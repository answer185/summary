# babel原理
## babel是什么
Babel 是一个 JavaScript 编译器。他把最新版的javascript编译成当下可以执行的版本

## 运行原理
有三个运行阶段，一个是解析阶段，一个是转换阶段，及一个生成阶段

### 解析
解析步骤接收代码并输出 AST。 这个步骤分为两个阶段：词法分析（Lexical Analysis） 和 语法分析（Syntactic Analysis）。

#### 1.词法分析

词法分析阶段把字符串形式的代码转换为令牌（tokens） 流。

你可以把令牌看作是一个扁平的语法片段数组：
如
>  n * n;

传转化成：
```
[
  { type: { ... }, value: "n", start: 0, end: 1, loc: { ... } },
  { type: { ... }, value: "*", start: 2, end: 3, loc: { ... } },
  { type: { ... }, value: "n", start: 4, end: 5, loc: { ... } },
  ...
]
```
每一个 type 有一组属性来描述该令牌：

```
{
  type: {
    label: 'name',
    keyword: undefined,
    beforeExpr: false,
    startsExpr: true,
    rightAssociative: false,
    isLoop: false,
    isAssign: false,
    prefix: false,
    postfix: false,
    binop: null,
    updateContext: null
  },
  ...
}
```
和 AST 节点一样它们也有 start，end，loc 属性。

#### 2.语法分析

语法分析阶段会把一个令牌流转换成 AST 的形式。 这个阶段会使用令牌中的信息把它们转换成一个 AST 的表述结构，这样更易于后续的操作。

简单来说，解析阶段就是：
> code(字符串形式代码) -> tokens(令牌流) -> AST（抽象语法树）

Babel 使用 @babel/parser 解析代码，输入的 js 代码字符串根据 ESTree 规范生成 AST（抽象语法树）。Babel 使用的解析器是 babylon。

### 转换
转换步骤接收 AST 并对其进行遍历，在此过程中对节点进行添加、更新及移除等操作。 这是 Babel 或是其他编译器中最复杂的过程。

Babel提供了@babel/traverse(遍历)方法维护这AST树的整体状态，并且可完成对其的替换，删除或者增加节点，这个方法的参数为原始AST和自定义的转换规则，返回结果为转换后的AST。

### 生成 
代码生成步骤把最终（经过一系列转换之后）的 AST 转换成字符串形式的代码，同时还会创建源码映射（source maps）。

代码生成其实很简单：深度优先遍历整个 AST，然后构建可以表示转换后代码的字符串。

Babel使用 @babel/generator 将修改后的 AST 转换成代码，生成过程可以对是否压缩以及是否删除注释等进行配置，并且支持 sourceMap。

![image](http://note.youdao.com/yws/res/96822/B23CB42135F44EBBBD41EE3696B5FBC1)

## 实践前提
### babel-core

babel-core是Babel的核心包,里面存放着诸多核心API,这里说下transform。

transform : 用于字符串转码得到AST 。

### babel-types

Babel Types模块是一个用于 AST 节点的 Lodash 式工具库，它包含了构造、验证以及变换 AST 节点的方法。 该工具库包含考虑周到的工具方法，对编写处理AST逻辑非常有用。

### Visitors (访问者)

当我们谈及“进入”一个节点，实际上是说我们在访问它们， 之所以使用这样的术语是因为有一个访问者模式（visitor）的概念。

访问者是一个用于 AST 遍历的跨语言的模式。 简单的说它们就是一个对象，定义了用于在一个树状结构中获取具体节点的方法。 这么说有些抽象所以让我们来看一个例子。

```

const MyVisitor = {
  Identifier() {
    console.log("Called!");
  }
};

// 你也可以先创建一个访问者对象，并在稍后给它添加方法。
let visitor = {};
visitor.MemberExpression = function() {};
visitor.FunctionDeclaration = function() {}
```
注意： Identifier() { ... } 是 Identifier: { enter() { ... } } 的简写形式


这是一个简单的访问者，把它用于遍历中时，每当在树中遇见一个 Identifier 的时候会调用 Identifier() 方法。

### Paths（路径）
AST 通常会有许多节点，那么节点直接如何相互关联呢？ 我们可以使用一个可操作和访问的巨大可变对象表示节点之间的关联关系，或者也可以用Paths（路径）来简化这件事情。

Path 是表示两个节点之间连接的对象。

在某种意义上，路径是一个节点在树中的位置以及关于该节点各种信息的响应式 Reactive 表示。 当你调用一个修改树的方法后，路径信息也会被更新。 Babel 帮你管理这一切，从而使得节点操作简单，尽可能做到无状态。

Paths in Visitors（存在于访问者中的路径）

当你有一个 Identifier() 成员方法的访问者时，你实际上是在访问路径而非节点。 通过这种方式，你操作的就是节点的响应式表示（译注：即路径）而非节点本身。

```
const MyVisitor = {
  Identifier(path) {
    console.log("Visiting: " + path.node.name);
  }
};

```
### Babel插件规则
Babel的插件模块需要我们暴露一个function,function内返回visitor对象。

```
//函数参数接受整个Babel对象,这里将它进行解构获取babel-types模块,用来操作AST。

module.exports = function({types:t}){

    return {
        visitor:{
            
        }
    }
    
}
```

## 插件实例
### 功能
做一个简单的ES6转ES3插件:
1. let,const 声明 -> var 声明  
2. 箭头函数 -> 普通函数

### 文件结构
```
|-- index.js  程序入口
|-- plugin.js 插件实现
|-- before.js 转化前代码
|-- after.js  转化后代码
|-- package.json  
```
#### package.json
npm init 生成 package.json
```
{
  "name": "babelplugin",
  "version": "1.0.0",
  "description": "create babel plugin",
  "main": "index.js",
  "scripts": {
    "babel": "node ./index.js"
  },
  "author": "webfansplz",
  "license": "MIT",
  "devDependencies": {
    "@babel/core": "^7.2.2"
  }
}

```
可以看到,我们首先下载了@babel/core作为我们的开发依赖,然后配置了npm run babel作为开发命令。

#### index.js
```

const { transform } = require('@babel/core');

const fs = require('fs');

//读取需要转换的js字符串
const before = fs.readFileSync('./before.js', 'utf8');

//使用babel-core的transform API 和插件进行字符串->AST转化。
const res = transform(`${before}`, {
  plugins: [require('./plugin')]
});

// 存在after.js删除
fs.existsSync('./after.js') && fs.unlinkSync('./after.js');
// 写入转化后的结果到after.js
fs.writeFileSync('./after.js', res.code, 'utf8');

````

### before.js
let code = 1;

可以在 https://astexplorer.net/ 上查看相应的结构
```
const a = 123;

let b = 456;
```

### plugin.js
```
module.exports = function({ types: t }) {
  return {
   //访问者
    visitor: {
     //我们需要操作的访问者方法(节点)
      VariableDeclaration(path) {
        //该路径对应的节点
        const node = path.node;
        //判断节点kind属性是let或者const,转化为var
        ['let', 'const'].includes(node.kind) && (node.kind = 'var');
      }
    }
  };
};
```

### 函数的plugin.js
```
module.exports = function({ types: t }) {
  return {
    visitor: {
      VariableDeclaration(path) {
        const node = path.node;
        ['let', 'const'].includes(node.kind) && (node.kind = 'var');
      },
      ArrowFunctionExpression(path) {
        let { id, params, body, generator, async } = path.node;
        //箭头函数我们会简写{return a+b} 为 a+b    
        if (!t.isBlockStatement(body)) {    
          const node = t.returnStatement(body);
          body = t.blockStatement([node]);
        }
        path.replaceWith(t.functionExpression(id, params, body, generator, async));
      }
    }
  };
};
```