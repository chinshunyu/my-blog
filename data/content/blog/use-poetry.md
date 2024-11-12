---
title: poetry使用指南
publishDate: 2024-11-11
description: 关于使用poetry的一点心得
---

python用户对conda这款流行的包管理工具一定不会感到陌生。然而，使用时间长了，就会对它极端的环境管理方案、臃肿的包感到困惑——anaconda过于臃肿，它默认为用户安装了众多用于科学计算的包，安装后大概5-6G。你可以选择anaconda的精简版：miniconda。它不包含默认的科学计算库，但miniconda仍然存在安装以来过于激进的问题。安装同样的包，conda可能会安装比别的包管理工具更多的依赖，这会导致项目环境不断膨胀。于是我决定开始使用poetry来管理虚拟环境。

## poetry 是什么

> https://python-poetry.org

Poetry 差不多是 pip + venv，的结合体。可以类似 pip 用于管理第三方模块的管理，但是比 pip 的功能强大许多，同时还包含 venv 的虚拟环境管理功能。大致的功能如下：

- 管理第三方模块的安装与卸载
- 管理虚拟环境
- 管理虚拟环境的依赖
- 管理打包与发布

## 名词解释：虚拟环境管理、模块管理、模块依赖管理

在开始之前，先介绍一下它们之间的关系。

### 虚拟环境

虚拟环境是指内建的 venv 或 virtualenv 、 conda 以及其他用来创建与管理 Python 虚拟环境的工具，不同的虚拟环境各自独立，存放的位置、安装的模块也都不一样。

### 模块管理、模块依赖管理

模块是指虚拟环境中安装的第三方模块及其版本。大多数项目对第三方库的版本都是有特定要求，如果对旧版本的项目使用新版本的依赖，可能会报很奇怪的错误。

当安装第三方模块时，第三方模块可能会安装自己依赖的模块。当安装两个以上模块时，就可能出现第三方模块的依赖出现冲突。这种情况一般是依赖的版本冲突。这种就叫做相关性依赖。

## pip 的不足

pip 是 python 内置的依赖管理工具，而它最大的不足在于 第三方模块的相关性依赖管理 的能力不足。尤其是在删除第三方模块是的依赖解析， 可以说是不会分析依赖。

我们看一个案例：

1、创建虚拟环境


```shell
~/coding> conda create -n myenv python=3.13
(myenv) ~/coding>
```

2、安装 flask 并查看安装好的第三方模块（依赖）

```shell
(myenv) ~/coding>pip install flask
(myenv) ~/coding>pip list
Package      Version
------------ -------
blinker      1.6.2
click        8.1.3
colorama     0.4.6
Flask        2.3.2
itsdangerous 2.1.2
Jinja2       3.1.2
MarkupSafe   2.1.2
pip          22.3.1
setuptools   65.5.0
Werkzeug     2.3.6
```

3、然后删除 flask 模块

```shell
(myenv) ~/coding>pip uninstall flask
Found existing installation: Flask 2.3.2
Uninstalling Flask-2.3.2:
  Would remove:
    /Users/username/anaconda3/envs/myvenv/lib/python3.13/site-packages/flask-2.3.2.dist-info\*
    /Users/username/anaconda3/envs/myvenv/lib/python3.13/site-package/flask\*
Proceed (Y/n)? y
  Successfully uninstalled Flask-2.3.2

(myvenv) ~/codingo>pip list
Package      Version
------------ -------
blinker      1.6.2[pyproject.toml](..%2F..%2F..%2Fpoetry-demo%2Fpyproject.toml)
click        8.1.3
colorama     0.4.6
itsdangerous 2.1.2
Jinja2       3.1.2
MarkupSafe   2.1.2
pip          22.3.1
setuptools   65.5.0
Werkzeug     2.3.6
```

然后就会发现，只少了 flask 模块，而安装 flask 时顺带安装的依赖全部被留下了。也就是说 pip 安装模块是，相关的依赖都会被下载安装。但是在删除时，pip 就不会进行管理了，而是直接把指定的模块移除，留下一堆依赖。

## 从零开始使用 Poetry

### 安装

poetry 是一个命令行工具，安装之后就可以使用 poetry 指令,可以将其安装全局环境或者是虚拟环境。

```shell
pip install poetry
```

安装之后就会在 python 解释器的安装目录下的 Scripts 目录里面出现 poetry.exe，因为在安装 python 解释器是配置过环境变量，然后就可以直接全局使用了。

### 初始化 poetry 项目

为了方便解说，我们先创建一个新的项目，名称为 poetry_test
指定都非常简单，建议跟着一步步自己也尝试一下。

1、初始化项目

```shell
~/coding>mkdir poetry_test
~/coding> cd poetry_test
~/coding/poetry_test> poetry init
```

然后会跳出来一连串的互动对话，用于创建项目的配置文件，这里我就直接全部一路回车，然后看一下生成的 pyproject.toml 配置文件：

```shell
[tool.poetry]
name = "poetry-test"
version = "0.1.0"
description = ""
authors = ["Your Name <you@example.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.13"


[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

此时项目的目录接口如下：

```shell
poetry_test
└── pyproject.toml

0 directories, 1 file
```

## 管理虚拟环境

poetry 预设了很多自己的虚拟环境配置，这些配置可以通过 poetry config 进行修改。
Macos 系统下 poetry 预设是将虚拟环境创建在 /Users/<用户名>/Library/Caches/pypoetry/virtualenvs 目录下，当用户在执行 poetry add 等指令时，poetry 都会自动检查当下是否正在使用虚拟环境：
- 如果是，则会直接安装模块到当前的虚拟环境下
- 如果否，则会自动帮你创建一个新的虚拟环境，然后再安装模块

## 创建虚拟环境

使用指令 poetry env use python：

```shell
─   ~/coding/poetry_test                                                                         82%   21:21  11.11  
╰ poetry env use python
Creating virtualenv poetry-test-ynfwcHPh-py3.13 in /Users/username/Library/Caches/pypoetry/virtualenvs
Using virtualenv: /Users/username/Library/Caches/pypoetry/virtualenvs/poetry-test-ynfwcHPh-py3.13
```

可以看出 poetry 为创建创建一个名为 poetry-test-ynfwcHPh-py3.13 的虚拟环境

### 重点说明

poetry env use python 是使用当前命令行下激活的 python 解释器创建虚拟环境
也可以将指令最后的 python ，改为 python3、python3.8，之类的，甚至只要需要 3.8，只要确保对于的解释器能够在环境变量中找到。
更多的配置可以查看 [官方文档](https://python-poetry.org/docs/managing-environments/)

poetry 默认会将虚拟环境统一放在指定目录，例如刚刚创建的项目就放在 /Users/username/Library/Caches/pypoetry/virtualenvs/ 目录当中。

虚拟环境的命名模式为 项目名-随机数-python版本。

## 在当前项目下创建虚拟环境

我们可以使用 poetry config --list 指令来查看 poetry 的几个主要设定：

```shell
╭─   ~/coding/poetry_test                                                                         82%   21:21  11.11  
╰ poetry config --list
cache-dir = "/Users/username/Library/Caches/pypoetry"
experimental.system-git-client = false
installer.max-workers = null
installer.modern-installation = true
installer.no-binary = null
installer.parallel = true
keyring.enabled = true
solver.lazy-wheel = true
virtualenvs.create = true
virtualenvs.in-project = null
virtualenvs.options.always-copy = false
virtualenvs.options.no-pip = false
virtualenvs.options.no-setuptools = false
virtualenvs.options.system-site-packages = false
virtualenvs.path = "{cache-dir}/virtualenvs"  # /Users/username/Library/Caches/pypoetry/virtualenvs
virtualenvs.prefer-active-python = false
virtualenvs.prompt = "{project_name}-py{python_version}"
warnings.export = true
```

其中 virtualenvs.create = true 若改为 false，则可以停止 poetry 在检查不到虚拟环境是自动创建的行为模式，但是建议不要改动。
而 virtualenvs.in-project = false 就是我们要修改的目标，使用指令：

```shell
poetry config virtualenvs.in-project true
```

先把之前创建的虚拟环境删除

```shell
╭─   ~/coding/poetry_test                                                                         82%   21:28  11.11  
╰ poetry env remove python
Deleted virtualenv: /Users/username/Library/Caches/pypoetry/virtualenvs/poetry-test-ynfwcHPh-py3.13
```

重新创建虚拟环境，看一下差异：

```shell
╭─   ~/coding/poetry_test                                                                         80%   21:44  11.11  
╰ poetry env use python3.12
Creating virtualenv poetry-test in /Users/username/coding/poetry_test/.venv
Using virtualenv: /Users/username/coding/poetry_test/.venv
```

可以看出：

- 虚拟环境的路径改为项目的根目录下了
- 名称固定位 .venv

## 启动与退出虚拟环境

在项目的根目录下使用 poetry shell 就可以进入到虚拟环境

```shell
╭─   ~/coding/poetry_test/.venv                                                                   80%   21:45  11.11  
╰ poetry shell
Spawning shell within /Users/username/coding/poetry_test/.venv

╭─   ~/coding/poetry_test/.venv                                                                          21:46  11.11  
╰ emulate bash -c '. /Users/username/coding/poetry_test/.venv/bin/activate'

╭─   ~/coding/poetry_test                                                                         80%   21:49  11.11  
╰ python --version
Python 3.12.3
```

poetry shell 指令会检查当前目录或上层目录是否存在 pyproject，toml 来确定需要启动的虚拟环境，所以如果不移动到项目的目录下，则会出现错误。

退出虚拟环境就更简单了，只要输入 exit 就可以了。

```shell
─   ~/coding/poetry_test                                                                         79%   21:49  11.11  
╰ exit
╭─   ~/coding/poetry_test/.venv                                                         02:59    79%   21:49  11.11  
╰ python --version
Python 3.11.5
```

## poetry 指令

poetry 是一个独立的命令行工具，他有自己的指令，需要花费额外的时间与精力学习，相较 pip 更加复杂，这个能是使用 poetry 的一道关卡。好在常用指令其实不超过 10 个，下面就来一一介绍。

### 安装模块

使用指令

```shell
poetry add
```

相较于 pip install，我们试试安装 flask 看看会有什么样的变化

```shell
─   ~/coding/poetry_test                                                               3.15s    79%   21:51  11.11  
╰ poetry add flask
Using version ^3.0.3 for flask

Updating dependencies
Resolving dependencies... (23.1s)

Package operations: 7 installs, 0 updates, 0 removals

  - Installing markupsafe (3.0.2)
  - Installing blinker (1.9.0)
  - Installing click (8.1.7): Failed

  TimeoutError

  The read operation timed out

  at ~/anaconda3/lib/python3.11/ssl.py:1167 in read
      1163│         if self._sslobj is None:
      1164│             raise ValueError("Read on closed or unwrapped SSL socket.")
      1165│         try:
      1166│             if buffer is not None:
    → 1167│                 return self._sslobj.read(len, buffer)
      1168│             else:
      1169│                 return self._sslobj.read(len)
      1170│         except SSLError as x:
      1171│             if x.args[0] == SSL_ERROR_EOF and self.suppress_ragged_eofs:

The following error occurred when trying to handle this error:


  ReadTimeoutError

  HTTPSConnectionPool(host='files.pythonhosted.org', port=443): Read timed out.

  at ~/anaconda3/lib/python3.11/site-packages/urllib3/response.py:753 in _error_catcher
       749│ 
       750│             except SocketTimeout as e:
       751│                 # FIXME: Ideally we'd like to include the url in the ReadTimeoutError but
       752│                 # there is yet no clean way to get at it from this context.
    →  753│                 raise ReadTimeoutError(self._pool, None, "Read timed out.") from e  # type: ignore[arg-type]
       754│ 
       755│             except BaseSSLError as e:
       756│                 # FIXME: Is there a better way to differentiate between SSLErrors?
       757│                 if "read operation timed out" not in str(e):

The following error occurred when trying to handle this error:


  ConnectionError

  HTTPSConnectionPool(host='files.pythonhosted.org', port=443): Read timed out.

  at ~/anaconda3/lib/python3.11/site-packages/requests/models.py:826 in generate
       822│                     raise ChunkedEncodingError(e)
       823│                 except DecodeError as e:
       824│                     raise ContentDecodingError(e)
       825│                 except ReadTimeoutError as e:
    →  826│                     raise ConnectionError(e)
       827│                 except SSLError as e:
       828│                     raise RequestsSSLError(e)
       829│             else:
       830│                 # Standard file-like object.

Cannot install click.

  - Installing itsdangerous (2.2.0)
  - Installing jinja2 (3.1.4)
  - Installing werkzeug (3.1.3)
```

可以看到 poetry 会将所有的信息全部列出来，并且清楚的告知了新增了那些第三方模块。
(由于作者现在窝在列车卧铺上码字，网速相当感人，故请无视timeout的报错)
此时项目中的 pyproject.toml 也发生了变化

```shell
[tool.poetry]
name = "poetry-test"
version = "0.1.0"
description = ""
authors = ["Your Name <you@example.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.11"
flask = "^3.0.3"


[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

这里要说明，安装 flask ，则 pyproject.toml 只会新增 flask = "^3.0.3" 这个字段的第三方模块，其余依赖不会出现在 toml 文件中。
这里是一个非常大的优点，以便区分哪些是用户安装的第三方模块，哪些是第三方模块一并安装的依赖。

### poetry.lock 与更新顺序

除了更新 pyproject.toml ，此时项目中还会新增一个文件，名为 poetry.lock ，它实际上就相当于 pip 的 requirements.txt ，详细记录了所有安装的模块与版本。

当使用 poetry add 指令时，poetry 会自动依序帮你做完这三件事：

更新 pyproject.toml。
依照 pyproject.toml 的内容，更新 poetry.lock 。
依照 poetry.lock 的内容，更新虚拟环境。
由此可见， poetry.lock 的内容是取决于 pyproject.toml ，但两者并不会自己连动，一定要基于特定指令才会进行同步与更新， poetry add 就是一个典型案例。

此时项目目录结构如下：

```shell
poetry-demo
├── poetry.lock
└── pyproject.toml

0 directories, 2 files
```

### poetry lock ：更新 poetry.lock

当你自行修改了 pyproject.toml 内容，比如变更特定模块的版本（这是有可能的，尤其在手动处理版本冲突的时候），此时 poetry.lock 的内容与 pyproject.toml 出现了脱钩，必须让它依照新的 pyproject.toml 内容更新、同步，使用指令：

```shell
poetry lock
```

如此一来，才能确保手动修改的内容，也更新到 poetry.lock 中，毕竟虚拟环境如果要重新建立，是基于 poetry.lock 的内容来安装模块，而非 pyproject.toml。

还是那句话：
> poetry.lock 相当于 Poetry 的 requirements.txt。

但要特别注意的是， poetry lock 指令，仅会更新 poetry.lock ，不会同时安装模块至虚拟环境.
因此，在执行完 poetry lock 指令后，必须再使用 poetry install 来安装模块。否则就会出现 poetry.lock 和虚拟环境不一致的状况。

## 新增模块至 dev-dependencies

有些模块，比如 pytest 、 black 等等，只会在开发环境中使用，产品的部署环境并不需要。

Poetry 允许你区分这两者，将上述的模块安装至 dev-dependencies 区块，方便让你轻松建立一份「不包含」 dev-dependencies 开发模块的安装清单。

在此以 Black 为例，安装方式如下：

```shell
poetry add black --group dev
```

结果的区别显示在 pyproject.toml 里：

```shell
[tool.poetry]
name = "poetry-test"
version = "0.1.0"
description = ""
authors = ["Your Name <you@example.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.11"
flask = "^3.0.3"


[tool.poetry.group.dev.dependencies]
black = "^24.10.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

可以看到 black 被列在不同区块： tool.poetry.dev-dependencies。

### Poetry 更新模块

这个就很简单了，使用 poetry update 指令即可：

```shell
poetry update
```

上面指令会更新全部可能可以更新的模块，也可以仅指定特定模块，比如：

```shell
poetry update requests toml
```

### 列出全部模块清单

类似 pip list ，这里要使用 poetry show.
这里的清单内容并不是来自于虚拟环境，这点和 pip 不同，而是来自于 poetry.lock 的内容。
你可能会想，来自于 poetry.lock 或虚拟环境，有差吗？两者不是应该要一致？
没错，理论上是，但也有不一致的时候，比如你使用了 pip install 指令安装模块，就不会记载在 poetry.lock 中，那 poetry show 自然也不会显示。

### 树状显示模块依赖层级

```shell
poetry show --tree
```

让主要模块与其依赖模块的关系与层次，一目了然。

而且很贴心的是，它也可以只显示指定模块的依赖层级，以 flask 为例：

```shell
╭─   ~/coding/poetry_test                                                                     1 ↵  77%   22:11  11.11  
╰ poetry show flask --tree 
flask 3.0.3 A simple framework for building complex web applications.
├── blinker >=1.6.2
├── click >=8.1.3
│   └── colorama * 
├── itsdangerous >=2.1.2
├── jinja2 >=3.1.2
│   └── markupsafe >=2.0 
└── werkzeug >=3.0.0
    └── markupsafe >=2.1.1 
```

### Poetry 移除模块

使用 poetry remove 指令。和 poetry add 一样，可以加上 -D 参数来移除置于开发区的模块。

而移除模块时的依赖解析能力，正是 Poetry 远优于 pip 的主要环节，因为 pip 没有嘛！也是我提议改用 Poetry 的关键理由——为了顺利移除模块与依赖。

前面已经提过，pip 的 pip uninstall 只会移除你所指定的模块，而不会连同依赖模块一起移除。

这是基于安全考量，因为 pip 没有依赖解析功能。如果贸然移除所有安装时一并安装的依赖模块，可能会造成巨大灾难，让别的模块失去效用。

所以，使用 pip 时，我们鲜少会去移除已经不再使用的模块。毕竟依赖关系错综复杂，移除模块可能造成许多副作用，实在是太麻烦了。

```shell
poetry remove flask
```

### 输出 Poetry 虚拟环境的 requirements.txt

理论上，全面改用 Poetry 后，项目中是不需要存在 requirements.txt ，因为它的角色已经完全被 poetry.lock 所取代。

但事实是，你可能还是需要它，甚至希望它随着 poetry.lock 的内容更新。

在 Poetry 的虚拟环境下，使用以往熟悉的指令 pip freeze > requirements.txt 就可以了。

```shell
pip freeze > requirements.txt
```

或者
```shell
poetry export -f requirements.txt -o requirements.txt --without-hashes
```

如果要输出输出 [tool.poetry.dev-dependencies] 的模块，可以添加--dev参数

```shell
poetry export -f requirements.txt -o requirements.txt --without-hashes --dev
```

### Poetry 常用指令清单

Poetry 的常用指令主要有下面几个：
- poetry add
- poetry remove
- poetry export
- poetry env use
- poetry shell
- poetry show
- poetry init
- poetry install

### 修改 poetry 镜像源

修改为清华镜像源

```shell
poetry source add tsinghua https://pypi.tuna.tsinghua.edu.cn/simple
```
或者在pyproject.toml文件最后添加如下内容：

```shell
[[tool.poetry.source]]
name = "tsinghua"
default = true
url = "https://pypi.tuna.tsinghua.edu.cn/simple"
```

---
*如果你觉得我的内容不错，考虑请我喝杯咖啡吗？*
*if you like what i do, maybe consider buying me a coffee/tea* 🥺👉👈

<p className="py-2 text-center mx-auto prose-p">
    <a href="https://buymeacoffee.com/johncachy" target="_blank" className="block text-center">
        <img src="https://cdn.buymeacoffee.com/buttons/v2/default-red.png" alt="Buy Me A Coffee" width="150"  className="mx-auto" style={{ marginTop: '20px' }} />
    </a>
</p>