---
title: AST抽象语法树
date: 2019-06-25 10:57:19
categories: js
tags: ast
---

## 抽象语法树

抽象语法树是编译原理中最上层的东西，但也可以看做是前端编译中最底层的东西，ast是指将代码文本通过一定的规则生成树结构的数据

前端编译中很多地方都用到抽象语法树，webpack的构建，babel编译，eslint校验，pretty代码美化等，利用抽象语法树我们可以在编译阶段做静态优化，当然，ast非常强大，理论上你可以做任何你想做的事情。

## 一个例子

简单代码：

```js
  var answer = 6;
```

生成的抽象语法树结构：

```json
  {
    "type": "Program",
    "body": [
        {
            "type": "VariableDeclaration",
            "declarations": [
                {
                    "type": "VariableDeclarator",
                    "id": {
                        "type": "Identifier",
                        "name": "answer"
                    },
                    "init": {
                        "type": "Literal",
                        "value": 6,
                        "raw": "6"
                    }
                }
            ],
            "kind": "var"
        }
    ],
    "sourceType": "script"
  }
```

[在线生成抽象语法树的网站](http://esprima.org/demo/parse.html#)，这是一个很好用的网站

如上所示，我们将一句声明变量并初始化的代码语句转换成了抽象语法树，现在有众多引擎可以很方便的做到这一点：chrome的v8，firefox的spidermonkey，以及npm上的众多包：

* esprima
* acron
* UglifyJS2
* babylon（大名鼎鼎的babel）
* recast

[AST对象文档](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey/Parser_API#Node_objects)

## 实践

大概了解了抽象语法树是什么，那我们用他来做什么呢？下面来做一件简单的事情：将上述`var answer = 6;`语法转换成`const answer = 6;`，你也可以用正则或字符串简单替换来完成这个任务，但是抽象语法树是一种更优雅的方式。

```js
  /** demo.js **/

  // 使用esprima将代码转换为ast
  const esprima = require('esprima')
  let code = 'var answer = 6'
  let ast = esprima.parseScript(code)
  console.log(ast)

  // 使用estraverse变量ast并修改
  const estraverse = require('estraverse')
  estraverse.traverse(ast, {
    enter: function (node) {
      node.kind = 'const'
    }
  })
  console.log(ast)

  // 使用escodegen将ast转换为代码文本
  const escodegen = require('escodegen')
  let transCode = escodegen.generate(ast)
  console.log(transCode)
```

运行上述代码`node demo.js`即可实现转换，其中使用`esprima`、`estraverse`、`escodegen`三个库帮助我们完成我们的需求

有时间可以看一下[esprima源码](https://github.com/jquery/esprima)

## AST三板斧

上述的实践我们做了三个步骤：

1. 使用esprima将源码转换为ast
2. 使用estraverse遍历和更新ast
3. 使用escodegen将ast转换为目标代码

实际上大部分的编译器都具备上述这三个基本过程，我们也可以自己实现一些自己的需求

下面是我自己发的一个npm包，基于recast引擎实现将代码中自定义函数修改为CommonJS规范的`exports.`导出形式

```js
  # 代码比较简单，可以直接看源码，就不多说明了
  $ npm i exportify-brightc -g
  $ exportify demo.js
```

## compiler

好奇编译器内部是怎么实现ast这部分的呢？

上面说三板斧分别是`Parsing`、`Transformation`、`Code Generation`，实际上简要描述过程是这样的：编译脚本逐个字符读取code源码字符串，进行词法分析（Lexical Analysis/tokenizer），生成词元（token）数组，再对词元数组做语法分析（Syntactic Analysis）生成ast结构，提供遍历方法对抽象语法树节点做遍历更新（你甚至可以生成一棵完全新的抽象语法树），最后再对新的ast按规则转换为目标代码字符串。很多高级语言编译为C执行时都是这么做的，基于这点，每个人实际上都可以发明一门自己的语言了。

这里有一个外国小哥写的lisp=>C的简单编译器，源码很简短，注释很详细，值得一看
[the-super-tiny-compiler](https://github.com/jamiebuilds/the-super-tiny-compiler)

## 总结

AST能做一些很强大的事情，比如代码批量转换、js静态优化等等，慢慢探索吧~
