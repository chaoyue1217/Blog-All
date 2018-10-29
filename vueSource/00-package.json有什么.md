---
title: package.json有什么
date: 2018-06-06
categories: vue-SourceCode
---

# 1. 必填配置

- name: 项目名称
- version: 项目版本号

```json
{
  name: "npm_work",
  version: "1.0.0"
}
```

# 2. 选填配置

## 2.1 scripts

指定了一系列npm的脚本命令
如以下配置则是指定了执行npm run dev、npm run build等时需要执行的命令。
npm run dev相当于运行了node bulid/dev-server.js
```
"scripts": {
    "dev": "node build/dev-server.js",
    "build": "node build/build.js",
    "test": "npm run unit",
    "lint": "eslint --ext .js,.vue src test/unit/specs"
  },
```

## 2.2 项目依赖

项目依赖有两种：
- 生产环境依赖： dependencies   命令： `npm install MODUL --save`
- 开发环境依赖： devDependencies   命令：`npm install MODUL --save-dev`

## 2.3 其他

- main： 入口文件，一般为index.js
- description：项目描述----字符串
```
如果 package.json 中没有 description 信息，npm 使用项目中的 README.md 的第一行作为描述信息。
```
- keywords：关键字----字符串数组
- author: 作者信息
- license: 默认是MIT
- bugs： 当前项目的错误信息

