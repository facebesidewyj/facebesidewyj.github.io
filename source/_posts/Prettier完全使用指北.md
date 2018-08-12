---
title: Prettier完全使用指北
date: 2018-04-15 22:05:55
categories: 团队规范构建
tags:
     - 规范
description: Prettier教程
---
# Prettier完全使用指北

## What is Prettier？

* 可以自己执行的格式化工具
* 支持多种语言
* 集成众多编辑器
* 少量的设置

## Advantages

* 保存即自动格式代码，书写代码无需关注格式
* 无需在code review中审查代码格式
* 提高效率，节省时间
* ....

## Get Started

1. 安装Prettier

```
npm install --save-dev --save-exact prettier
```

2. 在package.json中添加script

```json
{
  "scripts": {
    "format": "prettier --write **/*.{js,vue}"
  }
}
```

3. 创建一个配置文件.prettierrc，实例如下，后面有详细配置

```json
{
  "printWidth": 100,
  "singleQuote": true,
  "trailingComma": "es5"
}
```

4. 最后运行脚本

```
npm run format
```

## Ignore Files 

Prettier会默认忽略node_modules目录下的代码，配置.prettierignore可忽略其他文件。

## .prettierrc文件详解

```json
{
    "useTabs": false,      // 制表符缩进
    "printWidth": 100,     // 换行长度
    "tabWidth": 2,         // 缩进空格数
    "singleQuote": false,  // 使用单引号
    "semi": true,          // 末尾分号设置
    /**
     * 最后一个对象元素加逗号
     * 选项:
     *   - "none" - 不添加
     *   - "es5" - ES5中末尾添加逗号
     *   - "all" - 全部添加 
     */
    "trailingComma": "none",
    "bracketSpacing": true,       // 对象，数组加空格
    "jsxBracketSameLine": false,  // JSX > 后是否另起一行
    "arrowParens": "always", // (x) => {}箭头函数值有一个参数是否要有小括号
    /**
     * Prettier解析器设置
     * 选项:
     *   - "flow" 流的形式
     *   - "babylon" babel解析器
     */
    "parser": "babylon",
    "requirePragma": false,   // 是否要注释来决定是否格式化代码
    "proseWrap": "preserve"   // 代码超出是否要换行 preserve保留
}
```