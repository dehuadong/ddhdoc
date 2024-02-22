# 安装与使用配置

## 1、npm 全局安装 TypeScript

```sh
npm install -g typescript
```

**查看 TypeScript 是否安装成功**

```sh
tsc -v
Version 4.1.3
```

## 2、TypeScript 使用

### 2.1 通过全局 tsc 命令编译 TypeScript 代码

**编译**

```shell
tsc hello.ts
```

**监听编译**

```shell
tsc -w hello.ts
```

### 2.2 工程化编译方案

```shell
npm init # 初始化npm项目package.json文件
```

```shell
tsc -init # tsc 命令进行初始化配置tsconfig.json
```

**修改编译选项配置**

```json
{
  "compilerOptions": {
    "target": "ES6" /* tsc把代码编译成哪个ECMAScript目标版本 "ES3"（默认）， "ES5"， "ES6"， "ES2016"， "ES2017" 或 "ESNext"*/,
    "module": "CommonJS", /* tsc把代码编译成哪个模块系统: 'commonjs', 'amd', 'system', 'umd' or 'es2015'*/,
    "lib": ["ES6"], // 编译过程中需要引入的库
    "strict": true, // 启用所有严格类型检查选项
    "allowJs": true, // 允许编译 JavaScript 文件
    "esModuleInterop": true, // ES 模块互操作性,当启用时，将同时启用 allowSyntheticDefaultImports。
    "moduleResolution": "node", // 指定模块解析策略
    "resolveJsonModule": true, // 允许导入带有“.json”扩展名的模块
    "forceConsistentCasingInFileNames": true, // 禁止对同一个文件的不一致的引用
    "skipLibCheck": true, // 忽略所有的声明文件（*.d.ts）的类型检查
    "noUnusedLocals": true, // 若有未使用的局部变量则抛错
    "noUnusedParameters": true, // 若有未使用的参数则抛错
    "noImplicitReturns": true, // 不是函数的所有返回路径都有返回值时报错
    "declaration": true, // 生成相应的 .d.ts 文件
    "noEmitOnError": true, // 发送错误时不输出任何文件
    "declarationDir": "dist/types", // 生成声明文件的输出路径
    "outDir": "dist", // 输出目录
    "sourceMap": true // 生成相应的 '.map' 文件
  },
  "include": ["src/**/*"]
}
```

**使用[ts-node](https://github.com/TypeStrong/ts-node)直接运行 ts 项目**

```shell
# Locally in your project.
npm install -D typescript
npm install -D ts-node
# Or globally with TypeScript.
npm install -g typescript
npm install -g ts-node
# Depending on configuration, you may also need these
npm install -D tslib @types/node
```

**在 package.json 文件中，加入 script 命令以及入口文件**

```json
{
  "name": "hello",
  "version": "1.0.0",
  "description": "",
  "main": "src/index.ts",
  "scripts": {
    "dev": "ts-node src/index.ts",
    "start": "tsc -w",
    "build": "tsc",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

**运行编译命令**

```shell
npm run dev
npm run build
```

## 3、用 ESLint 规范 Typescript 代码

> ESLint 原生的规则和 @typescript-eslint/eslint-plugin 的规则太多了，而且原生的规则有一些在 TypeScript 中支持的不好，需要禁用掉。

> 为了提高工作效率，这里推荐使用 [AlloyTeam ESLint 规则中的 TypeScript 版本](https://github.com/AlloyTeam/eslint-config-alloy#typescript)，它已经为我们提供了一套完善的配置规则，并且与 Prettier 是完全兼容的（eslint-config-alloy 不包含任何代码格式的规则，代码格式的问题交给更专业的 Prettier 去处理）。

```shell
npm install --save-dev eslint typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-config-alloy
```

根目录下创建 .eslintrc.js

```javascript
module.exports = {
  extends: ['alloy', 'alloy/typescript'],
  env: {
    // 您的环境变量（包含多个预定义的全局变量）
    // Your environments (which contains several predefined global variables)
    //
    // browser: true,
    // node: true,
    // mocha: true,
    // jest: true,
    // jquery: true
  },
  globals: {
    // 您的全局变量（设置为 false 表示它不允许被重新赋值）
    // Your global variables (setting to false means it's not allowed to be reassigned)
    //
    // myGlobal: false
  },
  rules: {
    // 自定义您的规则
    // Customize your rules
  },
};
```

更多的使用方法，请参考 [AlloyTeam ESLint 规则](https://github.com/AlloyTeam/eslint-config-alloy)

## 4、如何结合 Prettier 使用

> eslint-config-alloy 从 v3 开始，已经不包含所有样式相关的规则了，故不需要引入 eslint-config-prettier。只需要安装 prettier 及相关 VSCode 插件即可。

```shell
npm install --save-dev prettier
```

AlloyTeam 提供了一套 Prettier 配置，你可以创建一个 .prettierrc 文件，直接复用此配置：

`"eslint-config-alloy/.prettierrc.js"`

VSCode 的一个最佳实践就是通过配置 .vscode/settings.json 来支持自动修复 Prettier 和 ESLint 错误：

```json
{
  "files.eol": "\n",
  "editor.tabSize": 2,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "[jsonc]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "vue",
    "typescript",
    "typescriptreact"
  ],
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```
