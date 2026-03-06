# 📅 Day 1-2：基本工具安装与环境配置
**学习日期：** 2026年2月2日 - 2月3日  
**任务来源：** QG2026 AI 组寒假训练营  

---

## 🎯 今日学习目标

| 序号 | 目标 | 完成状态 |
|------|------|----------|
| 1 | 体验 Orange Data Mining 可视化建模 | ✅ |
| 2 | 安装 Anaconda + PyCharm + Jupyter | ✅ |
| 3 | 学习 Markdown 基础语法 | ✅ |
| 4 | 配置 PicGo 图床（阿里云 OSS） | ✅ |
| 5 | 掌握 Git 基础操作 | ✅ |

---

## 一、Orange Data Mining 可视化建模体验

### 1.1 什么是 Orange？
Orange 是一个**基于 Python 的可视化机器学习平台**，通过拖拽"组件"（Widget）的方式完成数据分析和机器学习建模，无需写代码。

- 官网：https://orangedatamining.com/
- 特点：拖拽式编程、适合入门、可视化效果好

### 1.2 Orange 典型工作流
```
[File / CSV] → [Data Table] → [Preprocess] → [Feature Statistics]
                                    ↓
[Select Columns] → [Logistic Regression / Random Forest] → [Test & Score] → [Confusion Matrix]
```

### 1.3 体验步骤

**第一步：安装并启动**
1. 安装 Orange：`pip install Orange3`
2. 启动：命令行输入 `orange-canvas`（Windows 问题见 1.4）

**第二步：加载 iris 数据集**
3. 从左侧 Widget 面板的 **Data** 分类中，将 `File` 组件拖入画布
4. **双击 `File` 组件**，打开设置面板
   - 点击右侧文件夹图标 📁，浏览选择 iris.csv
   - 或者：点击下拉框，Orange 内置了 `iris.tab` 数据集，可直接选择，无需手动找文件
   - 加载成功后底部状态栏会显示 `150 instances, 5 features`（150行数据，5列特征）

**第三步：连接组件（搭建工作流）**
5. 从 **Visualize** 分类拖入 `Scatter Plot`，**将鼠标悬停在 `File` 组件的右侧输出端口**，拖动连线到 `Scatter Plot` 的输入端口
6. 从 **Model** 分类拖入 `Logistic Regression`，同样连接 `File` 的输出到它的输入
7. 从 **Evaluate** 分类拖入 `Test and Score`，将 `File` 连到它的 **Data** 输入，将 `Logistic Regression` 连到它的 **Learner** 输入

> 连线方式：鼠标悬停在组件边缘会出现橙色圆点，从圆点拖动到目标组件即可连线。

**第四步：查看结果**
8. **双击 `Scatter Plot`**：选择 X 轴为 `petal length`，Y 轴为 `petal width`，可以看到三种鸢尾花的分布（颜色区分）
9. **双击 `Test and Score`**：查看模型的准确率（Logistic Regression 在 iris 上通常达到 **~97%**）

**我的连接结果（截图）：**
```
[File] ──────────────────→ [Scatter Plot]
  │
  ├── (Data) ──────────→ [Test and Score]
  │
[Logistic Regression] → (Learner) ─┘
```

### ⚠️ 1.4 启动问题排查（Windows / Microsoft Store Python）

执行 `orange-canvas` 时遇到两个问题：

**问题 1：`ModuleNotFoundError: No module named 'chardet.universaldetector'`**

- **原因详解：**

  `chardet` 是一个用于自动检测文本编码（如 UTF-8、GBK、Latin-1 等）的 Python 库。

  | chardet 版本 | 内部结构 | 说明 |
  |---|---|---|
  | **≤ 4.x（旧版）** | 有 `chardet/universaldetector.py` 子模块 | `from chardet.universaldetector import UniversalDetector` ✅ |
  | **≥ 5.x（新版）** | 重构了内部架构，`universaldetector` 不再作为独立子模块暴露 | `from chardet.universaldetector import UniversalDetector` ❌ |

  Orange3 的源码（`io_util.py` 第 15 行）写死了旧版的导入方式：
  ```python
  # Orange3 内部代码（开发者写于 chardet 4.x 时代，未跟进更新）
  from chardet.universaldetector import UniversalDetector
  ```
  当系统安装了 `chardet 5.x` 或 `7.x` 时，该子模块路径不存在，导致整个 Orange 包无法导入，`orange-canvas` 自然也无法启动。

  > 💡 这是一个典型的**依赖版本不兼容**问题：上游库（chardet）进行了破坏性重构（breaking change），而下游库（Orange3）没有及时适配。

- **解决：降级 chardet 到兼容版本（4.0.0）**
  ```bash
  pip install "chardet==4.0.0" --force-reinstall
  ```
  `--force-reinstall` 的作用是强制卸载当前版本并重新安装指定版本，即使版本号相同也会重装，确保替换干净。

**问题 2：`orange-canvas` 命令无法识别**

- 原因：Microsoft Store 版 Python 将用户安装包的 Scripts 目录（`%LOCALAPPDATA%\Packages\...\Scripts`）未自动加入 PATH
- 解决方案 A（临时）：用完整路径启动
  ```powershell
  & "$env:LOCALAPPDATA\Packages\PythonSoftwareFoundation.Python.3.13_qbz5n2kfra8p0\LocalCache\local-packages\Python313\Scripts\orange-canvas.exe"
  ```
- 解决方案 B（永久）：将上述 Scripts 目录添加到用户 PATH 环境变量，重新打开终端后 `orange-canvas` 即可直接使用

### 1.4 学习心得
> Orange 让我直观地感受到了"数据流"的概念：数据从左到右流动，每个组件完成一步处理。
> 这和后续学习的 `sklearn Pipeline` 思想是一致的。

---

## 二、开发环境安装

### 2.1 Anaconda 安装配置

**作用：** Python 科学计算发行版，内置 NumPy、Pandas、Matplotlib 等常用库。

```bash
# 下载地址
https://www.anaconda.com/download

# 安装后验证
conda --version
python --version

# 常用 conda 命令
conda create -n myenv python=3.10    # 创建虚拟环境
conda activate myenv                  # 激活环境
conda deactivate                      # 退出环境
conda install numpy pandas matplotlib # 安装包
conda env export > environment.yml   # 导出环境配置
conda env create -f environment.yml  # 从配置文件恢复
```

**⚠️ 注意：** 安装时勾选 "Add Anaconda to PATH"（或手动添加），确保命令行可访问。

### 2.2 PyCharm 安装配置

**作用：** JetBrains 出品的 Python 专业 IDE，代码补全、调试功能强大。

- 学生免费授权：https://www.jetbrains.com/student/
- 配置解释器：File → Settings → Project → Python Interpreter → 选择 Anaconda 环境

### 2.3 Jupyter Notebook / JupyterLab 安装

```bash
# 安装
conda install jupyter jupyterlab

# 启动 Notebook
jupyter notebook

# 启动 Lab（推荐，界面更现代）
jupyter lab
```

**快捷键整理：**
| 快捷键 | 功能 |
|--------|------|
| `Shift + Enter` | 运行当前 Cell 并跳转下一个 |
| `Ctrl + Enter` | 运行当前 Cell，不跳转 |
| `A` | 在上方插入 Cell |
| `B` | 在下方插入 Cell |
| `DD` | 删除当前 Cell |
| `M` | 切换为 Markdown Cell |
| `Y` | 切换为 Code Cell |

---

## 三、Markdown 语法学习

### 3.1 标题
```markdown
# 一级标题
## 二级标题
### 三级标题
```

### 3.2 文字样式
```markdown
**加粗**
*斜体*
~~删除线~~
`行内代码`
```

### 3.3 列表
```markdown
- 无序列表项
  - 嵌套项
1. 有序列表项
```

### 3.4 表格
```markdown
| 列1 | 列2 | 列3 |
|-----|-----|-----|
| A   | B   | C   |
```

### 3.5 代码块
````markdown
```python
print("Hello World!")
```
````

### 3.6 链接与图片
```markdown
[链接文字](https://example.com)
![图片说明](图片路径或URL)
```

### 3.7 引用
```markdown
> 这是引用文字
```

---

## 四、PicGo 图床配置（阿里云 OSS）

### 4.1 为什么需要图床？
Markdown 中的图片默认是本地路径，上传到 GitHub 或分享给他人时图片会显示失败。
图床将图片上传到云端，生成可访问的 URL。

### 4.2 配置步骤
1. **注册阿里云**，开通 OSS 对象存储
2. 创建 Bucket（建议设置为公共读）
3. 获取 AccessKey ID 和 AccessKey Secret
4. 安装 PicGo：https://github.com/Molunerfinn/PicGo
5. 在 PicGo 中配置阿里云 OSS：
   - Region：华东1（oss-cn-hangzhou）等
   - AccessKeyId / AccessKeySecret
   - Bucket 名称
   - 存储路径：如 `img/`
6. 设置为默认图床，拖拽图片即可上传

---

## 五、Git 版本控制工具

### 5.1 Git 核心概念

| 概念 | 说明 |
|------|------|
| Repository（仓库） | 存放代码的目录 |
| Commit（提交） | 保存一次代码快照 |
| Branch（分支） | 独立的开发线路 |
| Remote（远程） | GitHub 等远程仓库 |
| Clone | 将远程仓库复制到本地 |
| Push | 将本地提交推送到远程 |
| Pull | 从远程拉取更新 |

### 5.2 Git 基础命令
```bash
# 初始化仓库
git init

# 配置用户信息（首次使用）
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"

# 查看状态
git status

# 添加文件到暂存区
git add .          # 添加所有文件
git add 文件名     # 添加指定文件

# 提交
git commit -m "提交说明"

# 关联远程仓库
git remote add origin https://github.com/用户名/仓库名.git

# 推送
git push -u origin main   # 首次推送
git push                  # 后续推送

# 拉取更新
git pull

# 克隆仓库
git clone https://github.com/用户名/仓库名.git

# 查看提交历史
git log --oneline
```

### 5.3 .gitignore 文件配置
```gitignore
# Python 相关
__pycache__/
*.py[cod]
*.egg-info/
.env
venv/

# Jupyter
.ipynb_checkpoints/

# IDE
.idea/
.vscode/
*.swp

# 数据文件（大文件不上传）
*.csv
*.xlsx
data/
```

### 5.4 GitHub 操作实践

**任务1：创建 GitHub 账号并建立仓库**
1. 访问 https://github.com 注册账号
2. 点击右上角 "+" → "New repository"
3. 仓库名：`QG2026-Winter-Training`
4. 设置为 Public，勾选 "Add a README file"

**任务2：上传第一个文件**
```bash
git clone https://github.com/你的用户名/QG2026-Winter-Training.git
cd QG2026-Winter-Training
# 创建 README.md 文件并编写内容
git add .
git commit -m "Day1-2：添加环境配置笔记"
git push
```

---

## 六、作业完成情况

### ✅ Task 1：创建 GitHub 账号并建立仓库
- 仓库名：`QG2026-Winter-Training`
- 可见性：Public
- 初始化：包含 README.md

### ✅ Task 2：编写 Markdown 个人简介并上传
```markdown
# 👋 Hi, I'm 黄熙晨

## 🎓 About Me
- 🏫 就读于广东工业大学材料相关专业
- 🔬 对 AI 和数据科学充满热情
- 🌱 Currently learning: Python, Machine Learning, Deep Learning

## 🛠️ Skills
- Python 基础
- 数据分析：NumPy, Pandas, Matplotlib
- 机器学习入门

## 📊 正在学习
- QG2026 AI 组寒假训练营
- 目标：21天系统入门 AI 开发
```

### ✅ Task 3：Python 环境初始化
```python
# 验证环境
import sys
print(f"Python 版本：{sys.version}")
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
print("NumPy:", np.__version__)
print("Pandas:", pd.__version__)
print("环境配置完成！")
```

---

## 七、今日学习反思

### 🌟 收获
- 了解了 AI 工程师的工作流程（Orange → 可视化建模）
- 掌握了 Anaconda 虚拟环境管理的重要性
- Git 版本控制将贯穿整个学习过程

### ❓ 遇到的问题
- conda 换源：默认源下载速度慢，配置国内镜像加速

  > ⚠️ 踩坑记录（多次报错，逐步排查）：
  > - 清华源（`mirrors.tuna.tsinghua.edu.cn`）→ **HTTP 404**（conda 25.x 下 URL 格式失效）
  > - 中科大源（`mirrors.ustc.edu.cn`）→ **HTTP 403 Forbidden**（对本机网络环境限速/封锁）
  > - 最终改用**阿里云源**，成功解决：

  ```bash
  # 先清除所有已配置的坏源
  conda config --remove-key channels

  # 配置阿里云镜像（推荐，稳定）
  conda config --add channels https://mirrors.aliyun.com/anaconda/pkgs/main
  conda config --add channels https://mirrors.aliyun.com/anaconda/pkgs/free
  conda config --add channels https://mirrors.aliyun.com/anaconda/cloud/conda-forge
  conda config --set show_channel_urls yes

  # 清除之前损坏的缓存包（避免 InvalidArchiveError）
  conda clean --packages --tarballs -y

  # 验证配置后重新创建环境
  conda config --show channels
  conda create -n myenv python=3.10
  ```

  > 💡 **conda 虚拟环境不影响系统 Python！**
  > conda 环境是完全隔离的沙箱，`conda create -n myenv python=3.10` 创建的 3.10 环境
  > 与系统已有的 Python 3.13 完全独立，互不干扰。
  > `conda activate myenv` 只影响当前终端窗口，关闭终端或执行 `conda deactivate` 即可恢复。
- pip 换源：
  ```bash
  pip install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple/
  ```

### 📚 扩展阅读
- [Pro Git 书籍（免费中文版）](https://git-scm.com/book/zh/v2)
- [Markdown 官方语法指南](https://daringfireball.net/projects/markdown/syntax)
- [Conda 官方文档](https://docs.conda.io/en/latest/)

---

**📝 作业提交记录**  
- 提交时间：2026年2月3日  
- GitHub Commit：`Day1-2：工具安装与环境配置完成` 
