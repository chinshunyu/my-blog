---
title: UV: 更加现代化的Python包管理工具
publishDate: 2025-05-31
description: 从此抛弃conda
---


#### 安装 uv:
```bash
brew install uv
```
#### 查看 uv 版本:
```bash
uv -- version

```
#### 删除缓存文件 :
```bash
uv cache clean
```
#### 管理 python 版本:
- 显示当前环境中所有可用的 python 版本：
```bash
uv python list
```
这个命令会列出所有可以使用的 python 版本，包括了 conda 的所有你虚拟环境中的 python
- 安装指定版本的 python 解释器:
```bash
uv python install 3.12
```
这个命令会将指定版本的解释器下载到以下目录
```bash
/Users/username/.local/share/uv/python
```

- 设置要使用的 python 版本
```bash
uv python pin 3.13
```
这个命令会设置之后使用 uv 命令默认使用的 python 版本, 如果 conda 虚拟环境中有一个 3.13 版本的 python, 那么 uv 会使用这个虚拟环境中的 python 解释器，如果没有 conda，那么会使用 uv 自己管理的 `/Users/username/.Local/share/uv/python` 目录下的 python 解释器。

- 确认当前环境下 python 的版本：
```bash
uv run python --version
```

- 确认当前环境下 python 解释器的位置 
```bash
cd uv_test
uv run which python
```



#### 初始化项目

```bash
cd uv_test
uv python pin 3.13
uv init
```
- `uv init` 会 **根据你 pin 的 Python 版本（3.13）去找本地缓存有没有对应的 Python 3.13 解释器**。
    
- 如果本地没有（或者版本不完整），它会从官方渠道自动**下载对应的 Python 3.13**(下载到 `/Users/username/.Local/share/uv/python`)。
    
- 下载完成后，`uv init` 会在 `~/work/uv_test/` 目录下创建一个 `.venv/` 虚拟环境。
    
- `.venv/` 里的 Python 解释器就是那个官方的、刚下载好的 Python 3.13 版本。
- Python 解释器文件实际存放在缓存目录（ `/Users/username/.Local/share/uv/python`），`.venv/` 里的 Python 是软链接或者复制。
    
- 如果你切换到别的项目，pin 了不同版本的 Python，`uv` 会自动下载并管理不同版本，彼此隔离。


#### 安装包

```bash
uv add requests==版本号
```
**包安装的地方是在 `.venv/lib/python3.13/site-packages`**， `uv` 给你创建的虚拟环境用的是独立的包目录，不是直接用 Conda 的 `site-packages`，这很符合 Python 虚拟环境的设计 — 解释器共享，包隔离。
- 你用 `uv add` 安装的包（比如 `requests`, `certifi` 等）都被装到了这个独立的 `.venv/lib/python3.13/site-packages` 目录里；
    
- 也就是说，虽然 Python 解释器可能是用的 Conda 环境里的，但包是安装在你项目虚拟环境的专属目录，跟 Conda 其他环境隔离开来了。



#### 如果要发布一个开源项目，以前会把requirements.txt放进项目里，别人就会知道项目的依赖。使用uv应该如何操作？

用 `uv` 管理项目依赖时，推荐的开源项目依赖声明和发布方式跟传统 `requirements.txt` 略有不同。

---

##### 传统做法回顾

- 传统用 `pip`，会在项目根目录放 `requirements.txt`
    
- 别人用 `pip install -r requirements.txt` 一次性装依赖
    

---

##### 使用 `uv` 的推荐做法

###### 1. 使用 `uv.lock` 文件来锁定依赖

- 当你用 `uv add 包名` 安装依赖时，`uv` 会自动生成一个叫 `uv.lock` 的文件（类似 `poetry.lock` 或 `Pipfile.lock`）
    
- 这个文件详细记录了所有包的版本、hash 等，保证别人用完全一样的依赖环境
    

###### 2. 在项目里加入 `uv.lock`

- 把 `uv.lock` 加入到你的 Git 版本库（不要忽略）
    
- 这样别人拿到代码后，运行：
    `uv sync`
    
- `uv sync` 会根据 `uv.lock` 安装完全一致的依赖版本

###### 3. 导出requirements.txt
如果要让使用 pip 的人知道项目的依赖，可以导出 requirements..txt 文件
```bash
uv pip compile pyproject.toml -o requirements.txt
```

###### 4 .安装一个 uv 管理的项目的依赖
如果 clone 了一个 uv 管理的项目，想安装这个项目所有的依赖，只需运行
```bash
uv synv
```
这类似于以前的 `pip install -r reqiurements.txt`




#### 假如我拿到一个requirements.txt管理的项目，如何使用uv安装依赖？

拿到一个用 `requirements.txt` 管理的项目，想用 `uv` 来安装依赖，可以按下面步骤操作：

---

###### 1 . 创建并进入项目目录

```bash
`cd /path/to/project`
```

确保你在项目根目录，有 `requirements.txt` 文件。

---

###### 2. 用 `uv` 创建虚拟环境

```bash
uv init
```

这会帮你创建 `.venv` 虚拟环境，默认用系统或你的 pin 版本的 Python。

---

###### 3. 使用 `uv import` 导入 `requirements.txt`

`uv` 提供了导入命令，可以把 `requirements.txt` 的依赖转换成 `uv` 的管理格式：


```bash
uv import requirements.txt
```


执行后：

- `uv` 会读取 `requirements.txt` 里的依赖
    
- 生成对应的 `uv.lock`（锁定版本）
    
- 并安装所有依赖包到 `.venv`
    

---

###### 4. 安装依赖

通常 `uv import` 会自动安装依赖，如果没有，可以用：

```bash
uv sync
```
根据 `uv.lock` 来安装依赖，保证版本一致。


### 查看当前环境有哪些依赖包

```
cd uv_test
uv pip list
```
此时一定要确保命令行没有激活 conda 环境