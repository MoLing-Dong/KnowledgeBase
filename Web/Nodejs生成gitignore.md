# TIPS 快速生成 .gitignore

## 1. gitignore 命令行工具

gitignore 是一个命令行工具，它能够从 [GitHub’s gitignore templates](https://github.com/github/gitignore) 仓库中生成 .gitignore 文件。

### 1.1 安装

如果你已经安装了 Node.js，可以通过 npm 安装 `gitignore`：

```shell
npm install -g gitignore
```

### 1.2 使用方法

安装后，你可以使用 gitignore 命令来生成 .gitignore 文件。例如，为 Node.js 项目生成 `.gitignore`：

```shell

gitignore node
```

这条命令将从 GitHub 上下载最新的 Node.js .gitignore 模板并保存到当前目录。

## 2. gibo（Gitignore Boilerplates）

gibo 是另一个流行的命令行工具，用于快速生成 .gitignore 文件，它同样从 GitHub’s gitignore 模板仓库下载。

### 2.1 安装

你可以通过 brew (macOS) 或直接从源代码安装 `gibo`：

```shell
brew install gibo
```

或者，使用以下命令克隆并安装：

```shell

git clone https://github.com/simonwhitaker/gibo.git ~/bin/gibo
echo 'export PATH="$PATH:$HOME/bin/gibo"' >> ~/.bash_profile
source ~/bin/gibo/gibo-completion.bash
```

### 2.2 使用方法

要为特定技术栈生成 .gitignore 文件，只需运行：

gibo Node VisualStudioCode > .gitignore

这条命令会将 Node 和 Visual Studio Code 的模板合并到一个 .gitignore 文件中。

## 3. 使用 gitignore.io 的 CLI 工具

如果你经常使用 gitignore.io，在命令行中使用它也是可能的。它提供了一个 API，可以直接通过 curl 来使用：

```shell
curl -L -s https://www.toptal.com/developers/gitignore/api/node,visualstudiocode > .gitignore
```

这条命令将为 Node.js 和 Visual Studio Code 生成 .gitignore 文件。
