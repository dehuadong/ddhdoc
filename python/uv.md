## uv 是什么

uv 是一个一个非常快的Python包安装和解析器，用Rust编写。设计为代替 pip和pip-tools 的工具。

[github地址](https://github.com/astral-sh/uv)

[文档地址](https://docs.astral.sh/uv/)

## 安装

```shell
# On macOS and Linux.
curl -LsSf https://astral.sh/uv/install.sh | sh

# On Windows.
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# With pip.
pip install uv

# With pipx.
pipx install uv

# 更新到最新版本
uv self update
```

## 初始化一个新项目

uv 支持管理 Python 项目，这些项目在其 `pyproject.toml` 文件中定义了依赖项

使用 `uv init` 命令创建一个新的 Python 项目:

```console
$ uv init hello-world
$ cd hello-world
```

或者，在工作目录中初始化一个项目:

```console
$ mkdir hello-world
$ cd hello-world
$ uv init
```

uv 将创建以下文件:

```text
.
├── .python-version
├── README.md
├── main.py
└── pyproject.toml
```

`main.py` 文件包含一个简单的“Hello world”程序。用 `uv run` 命令运行项目:
```console
$ uv run main.py
Hello from hello-world!
```
注意！第一次运行项目命令（即 `uv run` 、 `uv sync` 或 `uv lock` ）时在项目根目录创建一个虚拟环境和 `uv.lock`文件
完整的列表将如下所示：

```text
.
├── .venv
│   ├── bin
│   ├── lib
│   └── pyvenv.cfg
├── .python-version
├── README.md
├── main.py
├── pyproject.toml
└── uv.lock
```


### Python版本的安装和管理

```powershell
# 显示当前已经安装的和可供安装的Python版本
uv python list
# 安装指定的Python版本（注意：安装的Python并非全局可用，在命令行工具中输入Python无反映）
uv python install 3.10 3.11 3.12
# 卸载指定的版本
uv python uninstall 3.10
```

### 环境管理

```shell
uv venv
# 默认使用最新的Python版本，也可为环境指定Python版本
uv venv --python 3.11
# 显式同步项目依赖,会自动查找或下载合适的 Python 版本，创建并设置项目的虚拟环境，构建完整的依赖列表并写入uv.lock 文件，最后将依赖同步到虚拟环境中
uv sync
```

### 管理依赖

```shell
# 在当前环境中安装包flask
uv add flask
# 在 requirements.txt 文件中声明的依赖可以使用 -r 选项添加到项目中
uv add -r requirements.txt
# 删除依赖
uv remove pandas 
# 要更改现有的依赖项，例如，使用不同的约束条件 httpx 
uv add "httpx>0.1.0" --upgrade-package httpx
# 升级所有锁定的包
uv lock --upgrade
# 要将单个包升级到最新版本，同时保留所有其他包的锁定版本
uv lock --upgrade-package <package>
```

### pip 接口

uv 提供了一个可插入的替代品，用于常见的 `pip` ， `pip-tools` 和 `virtualenv` 命令。

uv 通过增加高级功能扩展了它们的接口，例如依赖版本覆盖、平台无关的解析、可重复的解析、替代的解析策略等。

使用 `uv pip` 接口迁移到 uv，无需更改现有的工作流程，体验 10-100 倍的速度提升。

```shell
# 锁定以 pyproject.toml 声明的依赖项，将编译需求转换为平台无关的需求文件：
uv pip compile pyproject.toml --universal -o requirements.txt
```
参数 `--universal` 进行通用解析，尝试生成一个与所有操作系统、架构和 Python 实现都兼容的单一 requirements.txt 输出文件。

```shell
# 安装锁定的依赖项:
uv pip sync requirements.txt
```
