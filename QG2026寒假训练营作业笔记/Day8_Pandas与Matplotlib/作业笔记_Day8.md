# 📅 Day 8：Pandas + Matplotlib 库学习
**学习日期：** 2026年2月9日  
**任务来源：** QG2026 AI 组寒假训练营  

---

## 🎯 今日学习目标

| 序号 | 目标 | 完成状态 |
|------|------|----------|
| 1 | Pandas 读取数据与基础数据清洗 | ✅ |
| 2 | Matplotlib 绘制散点图、折线图、条形图、饼图 | ✅ |
| 3 | 完成练习：加州房价数据集处理 | ✅ |
| 4 | 完成练习：NumPy 正弦曲线绘制 | ✅ |

---

## 一、Pandas 基础

### 1.1 核心数据结构

| 类型 | 说明 | 类比 |
|------|------|------|
| `Series` | 一维带标签数组 | Excel 一列 |
| `DataFrame` | 二维表格数据 | Excel 整张表 |

```python
import pandas as pd
import numpy as np

# ── Series ──
s = pd.Series([1, 3, 5, 7, 9], index=["a","b","c","d","e"])
print(s["c"])     # 5
print(s[s > 4])   # 过滤：c:5, d:7, e:9

# ── DataFrame ──
df = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 22],
    "score": [88.5, 72.0, 95.3]
})
```

### 1.2 读取数据
```python
# 读取 CSV（最常用）
df = pd.read_csv("data.csv", encoding="utf-8")
df = pd.read_csv("data.csv", sep=",", header=0, index_col=0)

# 读取 Excel
df = pd.read_excel("data.xlsx", sheet_name="Sheet1")

# 读取 JSON
df = pd.read_json("data.json")

# 保存数据
df.to_csv("output.csv", index=False)  # index=False 不保存行索引
df.to_excel("output.xlsx", index=False)
```

### 1.3 数据探索
```python
df.head(5)          # 前5行
df.tail(5)          # 后5行
df.info()           # 数据类型、空值概况
df.describe()       # 数值列的统计摘要（均值、标准差等）
df.shape            # (行数, 列数)
df.columns          # 列名列表
df.dtypes           # 各列数据类型
df.isnull().sum()   # 各列空值数量
df.duplicated().sum()  # 重复行数量
```

### 1.4 数据访问
```python
# 访问列
df["name"]            # Series
df[["name", "age"]]   # DataFrame（双方括号）

# 访问行
df.loc[0]             # 按标签（行索引）访问
df.iloc[0]            # 按位置访问
df.loc[0:2, "name":"age"]  # 标签切片
df.iloc[0:2, 0:2]          # 位置切片

# 条件筛选
df[df["age"] > 25]
df[(df["age"] > 20) & (df["score"] >= 80)]
df[df["name"].isin(["Alice", "Charlie"])]
```

### 1.5 数据清洗
```python
# ── 处理空值 ──
df.dropna()                          # 删除含空值的行
df.dropna(subset=["age"])            # 只看某列
df.fillna(0)                         # 填充0
df["age"].fillna(df["age"].mean())   # 填充均值
df.fillna(method="ffill")            # 向前填充

# ── 类型转换 ──
df["age"] = df["age"].astype(int)
df["score"] = pd.to_numeric(df["score"], errors="coerce")  # 无法转换的变为 NaN

# ── 删除重复行 ──
df.drop_duplicates()
df.drop_duplicates(subset=["name"])

# ── 重命名列 ──
df.rename(columns={"name": "student_name"}, inplace=True)

# ── 删除列 ──
df.drop(columns=["score"])
```

### 1.6 分组聚合
```python
# groupby + 聚合函数
grouped = df.groupby("class")["score"].mean()
df.groupby("class").agg({"score": ["mean", "std", "count"]})

# 透视表
pivot = df.pivot_table(values="score", index="class", 
                       columns="gender", aggfunc="mean")

# apply 自定义函数
df["grade"] = df["score"].apply(lambda x: "A" if x >= 90 else "B")
```

---

## 二、Matplotlib 绘图

### 2.1 基本结构
```python
import matplotlib.pyplot as plt
import matplotlib
# 设置中文字体
matplotlib.rcParams['font.sans-serif'] = ['SimHei', 'Microsoft YaHei']
matplotlib.rcParams['axes.unicode_minus'] = False  # 负号显示正常

# 推荐使用面向对象 API
fig, ax = plt.subplots(figsize=(10, 6))  # figsize 单位：英寸
ax.plot(...)
ax.set_title("标题")
ax.set_xlabel("X轴")
ax.set_ylabel("Y轴")
ax.legend()
ax.grid(True, alpha=0.3)
plt.tight_layout()   # 自动调整间距
plt.savefig("output.png", dpi=150, bbox_inches="tight")
plt.show()
```

### 2.2 折线图
```python
x = np.linspace(0, 4*np.pi, 300)
y1 = np.sin(x)
y2 = np.cos(x)

fig, ax = plt.subplots(figsize=(10, 5))
ax.plot(x, y1, color="#E74C3C", linewidth=2, linestyle="-", label="sin(x)")
ax.plot(x, y2, color="#3498DB", linewidth=2, linestyle="--", label="cos(x)")

# 填充区域
ax.fill_between(x, y1, 0, where=(y1 >= 0), alpha=0.2, color="#E74C3C")
ax.fill_between(x, y1, 0, where=(y1 < 0), alpha=0.2, color="#3498DB")

ax.axhline(y=0, color="black", linewidth=0.8)  # 零线
ax.set_title("正弦与余弦曲线", fontsize=14, fontweight='bold')
ax.legend(fontsize=12)
```

### 2.3 散点图
```python
fig, ax = plt.subplots()
scatter = ax.scatter(df["x"], df["y"], 
                     c=df["class"],       # 按类别着色
                     s=df["size"] * 10,   # 点的大小
                     alpha=0.6,
                     cmap="viridis")
plt.colorbar(scatter)  # 颜色条
```

### 2.4 条形图
```python
categories = ["类别A", "类别B", "类别C"]
values = [45, 78, 62]

fig, ax = plt.subplots()
bars = ax.bar(categories, values, color=["#E74C3C", "#2ECC71", "#3498DB"],
              edgecolor="black", linewidth=0.8)

# 在柱子上标注数值
for bar, val in zip(bars, values):
    ax.text(bar.get_x() + bar.get_width()/2, bar.get_height() + 1,
            str(val), ha="center", va="bottom", fontweight="bold")
```

### 2.5 饼图
```python
labels = ["A", "B", "C", "D"]
sizes = [35, 25, 20, 20]
explode = (0.1, 0, 0, 0)  # A 突出显示

fig, ax = plt.subplots()
wedges, texts, autotexts = ax.pie(
    sizes, explode=explode, labels=labels,
    autopct="%1.1f%%", shadow=True,
    startangle=90, colors=["#FF6B6B", "#4ECDC4", "#45B7D1", "#96CEB4"]
)
```

### 2.6 子图布局
```python
fig = plt.figure(figsize=(15, 10))

# 方法1：subplot
ax1 = fig.add_subplot(2, 3, 1)  # 2行3列，第1个

# 方法2：subplots（推荐）
fig, axes = plt.subplots(2, 3, figsize=(15, 10))
ax = axes[0, 0]  # 第0行第0列

# 方法3：GridSpec（复杂布局）
from matplotlib.gridspec import GridSpec
gs = GridSpec(3, 3, figure=fig)
ax1 = fig.add_subplot(gs[0, :])    # 第0行，占全部列
ax2 = fig.add_subplot(gs[1, 0:2])  # 第1行，前两列
```

---

## 三、今日作业

### 作业1：正弦曲线绘制
```python
"""正弦曲线绘制  日期：2026年2月9日"""
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
matplotlib.rcParams['font.sans-serif'] = ['SimHei', 'Microsoft YaHei']
matplotlib.rcParams['axes.unicode_minus'] = False

def plot_sine_curve():
    x = np.linspace(0, 4 * np.pi, 300)
    y = np.sin(x)
    
    fig, ax = plt.subplots(figsize=(12, 5))
    ax.plot(x, y, color="#E74C3C", linewidth=2, label="sin(x)")
    
    # 正半周期填充红色，负半周期填充蓝色
    ax.fill_between(x, y, 0, where=(y >= 0), alpha=0.3, color="#E74C3C", label="正值区域")
    ax.fill_between(x, y, 0, where=(y < 0), alpha=0.3, color="#3498DB", label="负值区域")
    
    # 标注关键点
    key_x = [0, np.pi/2, np.pi, 3*np.pi/2, 2*np.pi]
    key_y = [0, 1, 0, -1, 0]
    ax.scatter(key_x, key_y, color="black", s=80, zorder=5)
    
    ax.axhline(y=0, color="black", linewidth=0.8, linestyle="--")
    ax.set_xlabel("x（弧度）")
    ax.set_ylabel("sin(x)")
    ax.set_title("正弦函数图像（0 ~ 4π）", fontsize=14, fontweight="bold")
    ax.set_xticks([0, np.pi/2, np.pi, 3*np.pi/2, 2*np.pi, 5*np.pi/2, 3*np.pi])
    ax.set_xticklabels(["0", "π/2", "π", "3π/2", "2π", "5π/2", "3π"])
    ax.legend()
    ax.grid(True, alpha=0.3)
    plt.tight_layout()
    plt.savefig("sine_curve.png", dpi=150)
    plt.show()

plot_sine_curve()
```

### 作业2：加州房价数据处理
```python
"""加州房价数据集处理"""
from sklearn.datasets import fetch_california_housing
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 加载数据集
housing = fetch_california_housing(as_frame=True)
df = housing.frame

# 1. 基本探索
print("数据形状：", df.shape)
print("\n各列统计摘要：")
print(df.describe().round(2))

# 2. 空值检查
print("\n空值数量：", df.isnull().sum().sum())

# 3. 相关性分析
corr_with_price = df.corr()["MedHouseVal"].sort_values(ascending=False)
print("\n与房价的相关系数：")
print(corr_with_price)

# 4. 可视化：房价分布
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

axes[0].hist(df["MedHouseVal"], bins=50, color="#3498DB", edgecolor="black", alpha=0.7)
axes[0].set_title("房价分布直方图")
axes[0].set_xlabel("房价中位数（万美元）")
axes[0].set_ylabel("频数")

axes[1].scatter(df["MedInc"], df["MedHouseVal"], 
                alpha=0.1, s=3, color="#E74C3C")
axes[1].set_title("收入 vs 房价散点图")
axes[1].set_xlabel("收入中位数（万美元）")
axes[1].set_ylabel("房价中位数（万美元）")

plt.tight_layout()
plt.savefig("california_housing.png", dpi=150)
plt.show()
```

---

## 四、知识点总结

### Pandas 核心操作速查

| 需求 | 代码 |
|------|------|
| 读CSV | `pd.read_csv("file.csv")` |
| 查看概况 | `df.info()`, `df.describe()` |
| 过滤行 | `df[df["col"] > 5]` |
| 选列 | `df[["col1", "col2"]]` |
| 分组聚合 | `df.groupby("col").mean()` |
| 填充空值 | `df.fillna(df.mean())` |
| 应用函数 | `df["col"].apply(func)` |

### Matplotlib 绘图速查

| 图表类型 | 函数 |
|----------|------|
| 折线图 | `ax.plot(x, y)` |
| 散点图 | `ax.scatter(x, y)` |
| 条形图 | `ax.bar(x, height)` |
| 横向条形图 | `ax.barh(y, width)` |
| 直方图 | `ax.hist(data, bins=20)` |
| 饼图 | `ax.pie(sizes, labels=...)` |
| 热力图 | `ax.imshow(matrix, cmap="hot")` |

### 🌟 今日收获
- Pandas 的 `groupby` + `agg` 是数据分析的核心操作
- Matplotlib 的面向对象 API 比 pyplot API 更灵活，大项目必用
- 数据可视化的第一步永远是 `df.info()` 和 `df.describe()`

---

**📝 作业提交记录**  
- 提交时间：2026年2月9日  
- GitHub Commit：`Day8：Pandas与Matplotlib学习完成`  

