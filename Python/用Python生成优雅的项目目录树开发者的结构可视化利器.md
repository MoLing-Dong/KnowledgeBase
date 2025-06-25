
# Project Tree

> 刚接手新项目一脸懵？文档里的目录结构总是过时？今天手把手教你用 30 行 Python 代码打造智能目录树生成器，自动过滤.git/venv/node_modules 等干扰项！

---

## 🔥 痛点场景：我们为什么需要它？

- 新人入职看项目：`node_modules`和`.git`占满屏幕，核心代码难定位
- 编写项目文档：手动维护目录结构，每次改动都要重新截图
- 代码审查时：在`__pycache__`和`target`文件夹里大海捞针
- 分享开源项目：想让别人一眼看懂你的精妙设计，却被开发环境文件干扰

---

## 🚀 核心功能：我的小工具能做什么？

```python
# 运行效果演示
$ python project_tree.py
Enter the project directory path: ./my_project
GenealogyCore
├── .mvn/
│   └── wrapper/
└── src/
    ├── main/
    │   ├── java/
    │   │   └── org/
    │   │       └── mol/
    │   │           └── genealogycore/
    │   │               ├── advice/
    │   │               ├── config/
    │   │               ├── controller/
    │   │               ├── exception/
    │   │               ├── interceptor/
    │   │               ├── mapper/
    │   │               ├── model/
    │   │               │   ├── bo/
    │   │               │   ├── dto/
    │   │               │   ├── mapper/
    │   │               │   ├── po/
    │   │               │   └── vo/
    │   │               ├── repository/
    │   │               ├── service/
    │   │               │   └── impl/
    │   │               ├── typehandler/
    │   │               └── utils/
    │   └── resources/
    │       ├── db/
    │       ├── graphql/
    │       ├── mapper/
    │       ├── static/
    │       └── templates/
    └── test/
        └── java/
            └── org/
                └── mol/
```

✅ **四重过滤系统**

- 基础排除：`.git` `.idea` `.vscode`
- Python 专杀：`__pycache__` `venv`
- Java 克星：`target` `out`
- Web 净化：`node_modules` `dist`

✅ **智能树形结构**

- 精准识别目录层级
- 自动判断分支节点
- 专业符号展示（├── └── │ ）

✅ **跨平台支持**\
Windows/macOS/Linux 通吃！

---

## 🛠️ 实现原理：代码里藏了哪些黑科技？

### 3.1 目录扫描引擎

```python
def generate_tree(directory, indent=""):
    # 获取当前层有效目录（已过滤）
    items = [entry for entry in os.listdir(directory)
            if os.path.isdir(os.path.join(directory, entry))]
    valid_items = [item for item in items if item not in ALL_EXCLUDE_DIRS]

    # 动态生成树形连接符
    for index, entry in enumerate(valid_items):
        is_last = index == len(valid_items) - 1
        connector = "└──" if is_last else "├──"
        new_indent = indent + ("    " if is_last else "│   ")
```

### 3.2 符号生成算法

    目录结构             打印符号
    parent/            ├── child1/
    ├── child1/         │   └── grandchild/
    └── child2/         └── child2/
        └── grandchild/

### 3.3 路径处理技巧

- 使用`os.path.join()`处理跨平台路径差异
- `os.path.basename()`提取纯净目录名
- 递归时传递缩进量保持层级关系

---

## 💡 高级用法：这样用更高效

1. **自定义排除列表**\
    在代码开头添加你的专属过滤规则：

    ```python
    CUSTOM_EXCLUDE = ["temp", "backup"]
    ALL_EXCLUDE_DIRS = ALL_EXCLUDE_DIRS.union(CUSTOM_EXCLUDE)
    ```

2. **团队共享配置**\
    创建`.treerc`配置文件：

    ```ini
    [exclude]
    java = target, bin
    web = coverage, .next
    ```

---

## 🌟 GitHub 完整源码

[👉 点击获取智能目录树生成器](https://github.com/MoLing-Dong/ITools/blob/master/ProjectTreeMake.py)

**推荐 Star 理由**：

- 开箱即用的`.bat`/`.sh`启动脚本
- 已处理 Windows 中文路径问题
- 包含单元测试用例
- 详细的类型注解

---

## 🎯 技术总结

通过这个项目我们学会了：

- 递归算法的实际应用场景
- `os.path`模块的妙用
- 命令行工具的交互设计
- 开发者体验(DevEx)的优化思路

**小彩蛋**：在你的`package.json`里添加这个吧！

```json
{
  "scripts": {
    "tree": "python ./scripts/project_tree.py"
  }
}
```

---

**互动话题**：\
你在项目中遇到过哪些奇葩的目录结构？快来评论区吐槽！🚀

本文使用 DeepSeek 生成
