# nodejs多版本管理

## 下载与安装

下载地址：[https://github.com/coreybutler/nvm-windows/releases](https://github.com/coreybutler/nvm-windows/releases)

安装前，这里有一点需要注意，如果以前安装过node，需要先卸载，并且要把目录清理干净。下面是官方给的说明：
**⭐ ⭐ Uninstall any pre-existing Node installations!! ⭐ ⭐**

> Uninstall any existing versions of Node.js before installing NVM for Windows (otherwise you'll have conflicting versions). Delete any existing Node.js installation directories (e.g., `%ProgramFiles%\nodejs`) that might remain. NVM's generated symlink will not overwrite an existing (even empty) installation directory.

> 👀 **Backup any global `npmrc` config** 👀 (e.g. `%AppData%\npm\etc\npmrc`)

> Alternatively, copy the settings to the user config `%UserProfile%\.npmrc`. Delete the existing npm install location (e.g. `%AppData%\npm`) to prevent global module conflicts.

::: tip
安装的时候需要制定两个目录，一个是nvm的安装目录，一个是建立node软连接的目录。

nvm的安装目录（绝对路径所有层级）中最好不要存在中文或者两个单词以上的路径，如Progrom Files等，否则可能导致命令运行出错。
:::

## nvm常用命令

```shell
# 查看已安装的nodejs版本
nvm list

# 查看所有可安装的nodejs版本
nvm list available

# 安装nodejs
nvm install 14 # 如果只填前面的数字，则默认安装本版本的最新版
nvm install 14.20.0 # 也可指定具体版本安装
nvm install 16.18.0

# 使用指定版本的nodejs（初次使用，需要指定版本）
nvm use 14.20.0 # 需使用完整版本号，才能指定成功

node -v # 输出版本号，则nodejs指定成功，可以开始使用

# 卸载指定nodejs版本
nvm uninstall 14.20.0

# 安装镜像
nvm node_mirror https://npmmirror.com/mirrors/node/
nvm npm_mirror https://npmmirror.com/mirrors/npm/

# 启用node版本
nvm use 14.20.0
# 重新安装yarn 
npm install -g yarn
# 切换node版本 
nvm use 16.18.0
# 重新安装yarn 
npm install -g yarn
```

## macOS 配置

1、打开终端输入：

```shell
brew install nvm
```

2、执行成功后，添加配置代码到zsh文件

```shell
vi ~/.zshrc
```



```
export NVM_DIR="$HOME/.nvm"
  [ -s "/usr/local/opt/nvm/nvm.sh" ] && \. "/usr/local/opt/nvm/nvm.sh"  # This loads nvm
  [ -s "/usr/local/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/usr/local/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion
```

3、添加成功后，重启终端

```sh
source ~/.zshrc
```

4、nvm常用命令

```sh
nvm on       #启用版本管理
nvm off      #禁用版本管理

nvm ls                  #查看本地 Node 版本
nvm ls-remote           #查看官网 Node 版本
nvm ls-remote --lts     #查看官网 Node LTS 版本

nvm current             #显示当前的版本
nvm install 16.18.1    #安装指定版本
nvm uninstall 16.18.1  #卸载指定版本
nvm use 16.18.1        #使用指定版本

nvm alias default 16.18.1  #设置默认使用版本
```

5、NPM 源设置

> 在国内有时候受限于网络因素的影响，通常在安装一个包管理器之前可以切换为 淘宝 NPM 镜像，加速安装速度，但是要注意如果是私有模块在 NPM 官方的，则必须切换为官方源，否则会出现 404 错误。

**查看当前 npm 源**

```sh
npm config get registry  # http://registry.npmjs.org/
```

**切换为 taobao 源**

```shell
npm config set registry=https://registry.npmmirror.com
```

**切换为 npm 官方源,在 npm publish 的时候 需要切换回 npm 源**

```shell
npm config set registry=http://registry.npmjs.org
```

**如果不想全局设置，执行 npm 命令时也可通过参数传递镜像地址**

```sh
npm i module --registry=https://registry.npmmirror.com
```

**编辑源**

```sh
npm config edit
```

`electron_mirror=https://npmmirror.com/mirrors/electron/`
`electron-builder-binaries_mirror=https://npmmirror.com/mirrors/electron-builder-binaries/`
`registry=https://registry.npmmirror.com/`

