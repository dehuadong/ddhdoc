# npm、yarn、pnpm 及 package.json 详解

## 1、npm 包管理

1、 自我更新 npm

> npm 更新速度很快，安装 node 附带的 npm 可能不是最新的，可以执行命令到最新版本 npm。install，安装；-g 全局安装；npm@latest 就是 `<packageName>`@`<version>`的格式

```shell
npm install npm@latest -g
```

2、 package.json 文件创建

npm init 就可以创建一个 package.json 文件

npm init --yes 可以跳过所有的提问

```shell
npm init

npm init --yes
```

npm 编辑源命令

```sh
npm config edit
```

npm 使用淘宝镜像

```sh
npm config set registry https://registry.npmmirror.com/
```

验证命令

```sh
npm config get registry
```

npm 切换回官方源

```shell
npm config set registry https://registry.npmjs.org/
```

使用强制清除缓存指令

```shell
npm cache clean --force
```

加入 electron 源

```shell
ELECTRON_MIRROR=https://npmmirror.com/mirrors/electron/
electron-builder-binaries_mirror=https://npmmirror.com/mirrors/electron-builder-binaries/

```

4、 npm install 参数

> 命令`npm install` 在本地 node_modules 文件夹中安装 package.json 中列为依赖项的所有模块。

```shell
-S(--save):下载的包加入dependencies 在npm5之后默认参数,不写-S或者--save也会加上 它比较重要,可以帮我们安装包的时候记录版本信息
-D(--save-dev):下载的包只在开发阶段加入,一般用于编译 在devDependencies中
-g(--global): 全局安装
--only=prod(--production): 只安装生产环境的包(dependencies)
--only=dev: 只安装开发环境的包(devDependencies)

```

```shell
npm install <package>@<version> # 使用 @ 语法来指定安装 npm 软件包版本
```

5、语义化版本号

```shell
^version:中小版本
^2.13.1->在2.x.x中取最新的版本
~version:小版本
~2.13.1->在2.13.x中取最新的版本
version:指定版本
2.13.1->啥也不加就取当前写的版本

```

6、其他常用命令

```shell
npm help # 查看npm命令列表
npm -l # 查看各个命令的简单用法
npm -v # 查看npm的版本
npm config list -l # 查看npm的配置
npm outdated # 查看package.json软件包的新版本，运行命令
npm list # 查看所有已安装的 npm 软件包（包括它们的依赖包）的最新版本
npm list -g  # 查看全局安装的软件包
npm list --depth=0 # 获取已经安装顶层的软件包
npm info # 查看模块具体信息
npm update # 更新次版本或补丁版本命令； aliases: up, upgrade, udpate
npm uninstall $module # 删除包，参数同install

npm [run] $script # 执行package.json 文件scripts 字段中的脚本
npm bin # 列出当前项目可执行脚本目录
npm config set prefix "E:/nodejs/npm_global" # 修改npm全局包路径
npm config set cache "E:/nodejs/npm_global/npm_cache" # 修改npm全局包缓存路径
# 查看则是把set改成get
```

## 使用 nrm 管理 registry 地址

### 1. 安装 nrm

```shell
npm install -g nrm
```

### 2. 查看可选源

```shell
 nrm ls
```

### 3. 查看当前源

```shell
nrm current
```

### 4. 切换 npm registry 地址

```shell
nrm use taobao
nrm use npm
```

### 5.查看 npm 的配置信息

```shell
npm config ls
```

## 登录 npm

先切换源，再登录发布包，必须使用 npm origin ，不能切换成别的镜像

```shell
npm adduser
```

### 查看账户命令

```shell
npm profile get
```

### 执行发布命令

```shell
npm publish --access public // 非公开发布
npm publish // 使用公开方式发布包
```

### 更新已经发布的包

### 修改版本号命令

```shell
npm version patch
```

### 发布命令

```shell
npm publish
```

> npm version 后面参数说明 ✨:
> patch：小变动，比如修复 bug 等，版本号变动 **v1.0.0->v1.0.1**
> minor：增加新功能，不影响现有功能,版本号变动 **v1.0.0->v1.1.0**
> major：破坏模块对向后的兼容性，版本号变动 **v1.0.0->v2.0.0**

### 从 npm 上面卸载自己发布的包

```shell
npm unpublish <package-name> -f
```

### git 存储库链接到包

TODO

## 2、yarn 安装及配置

### 2、1 安装

> 本次使用 npm 进行安装

```sh
npm install -g yarn
```

> 安装完后使用 yarn -v 可查看版本号

### 2、2 查看镜像配置

`yarn config get registry`

### 2、3 配置淘宝镜像

```shell
yarn config set registry https://registry.npmmirror.com/
yarn config set electron_mirror https://npmmirror.com/mirrors/electron/ -g
yarn config set electron-builder-binaries_mirror https://npmmirror.com/mirrors/electron-builder-binaries/ -g
yarn config set sass_binary_site https://npmmirror.com/mirrors/node-sass/ -g
```

### 2、4 查看全局安装文件目录

`yarn global dir`

### 2、5 查看当前配置

`yarn config list`

## 3、pnpm 包管理

[pnpm 包管理工具](https://pnpm.io/zh/) 快速的，节省磁盘空间的包管理工具

### 基本命令

```shell
pnpm install xxx
```

别名: i

pnpm install 用于安装项目所有依赖.

在 CI 环境中, 如果存在需要更新的 lockfile 会安装失败.

在 workspace 内, pnpm install 下载项目所有依赖. 如果想禁用这个行为, 将 recursive-install 设置为 false.

```shell
pnpm add  xxx
```

安装软件包及其依赖的任何软件包。 默认情况下，任何新软件包都安装为生产依赖项。
摘要：

| Command              | Meaning                       |
| -------------------- | ----------------------------- |
| `pnpm add sax`       | 保存到 `dependencies`         |
| `pnpm add -D sax`    | 保存到 `devDependencies`      |
| `pnpm add -O sax`    | 保存到 `optionalDependencies` |
| `pnpm add -g sax`    | Install package globally      |
| `pnpm add sax@next`  | 从 `next` 标签下安装          |
| `pnpm add sax@3.0.0` | 安装指定版本 `3.0.0`          |

```shell
pnpm update xxx
```

别名: `up`, `upgrade`

`pnpm update` 根据指定的范围更新软件包的最新版本。

在不带参数的情况下使用时，将更新所有依赖关系。摘要：

| Command              | Meaning                                                |
| -------------------- | ------------------------------------------------------ |
| `pnpm up`            | 遵循 `package.json` 指定的范围更新所有的依赖项         |
| `pnpm up --latest`   | 更新所有依赖项，此操作会忽略 `package.json` 指定的范围 |
| `pnpm up foo@2`      | 将 `foo` 更新到 v2 上的最新版本                        |
| `pnpm up "@babel/*"` | 更新 `@babel` 范围内的所有依赖项                       |
| `pnpm up "@babel/*"` | 更新 `@babel` 范围内的所有依赖项                       |
