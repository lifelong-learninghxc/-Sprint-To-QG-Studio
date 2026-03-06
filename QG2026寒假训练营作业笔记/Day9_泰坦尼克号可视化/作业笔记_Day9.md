# 📅 Day 9：综合实战 — 泰坦尼克号数据集多维可视化分析
**学习日期：** 2026年2月10日  
**任务来源：** QG2026 AI 组寒假训练营  

---

## 🎯 今日学习目标

| 序号 | 目标 | 完成状态 |
|------|------|----------|
| 1 | 综合运用 Pandas 进行数据清洗 | ✅ |
| 2 | 多维度可视化分析泰坦尼克号数据 | ✅ |
| 3 | 掌握 GridSpec 复杂布局 | ✅ |
| 4 | 完成至少 6 个不同维度的图表 | ✅ |

---

## 一、数据集介绍

### 1.1 数据字段说明
| 字段 | 说明 | 类型 |
|------|------|------|
| `Survived` | 是否存活（0=遇难, 1=生还） | 分类 |
| `Pclass` | 船票等级（1=一等, 2=二等, 3=三等） | 有序分类 |
| `Sex` | 性别（male/female） | 分类 |
| `Age` | 年龄 | 连续数值 |
| `SibSp` | 同船兄弟姐妹/配偶数量 | 整数 |
| `Parch` | 同船父母/子女数量 | 整数 |
| `Fare` | 船票价格 | 连续数值 |
| `Embarked` | 登船港口（S/C/Q） | 分类 |

### 1.2 数据加载
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib
from matplotlib.gridspec import GridSpec

matplotlib.rcParams['font.sans-serif'] = ['SimHei', 'Microsoft YaHei']
matplotlib.rcParams['axes.unicode_minus'] = False

def load_titanic_data():
    """加载泰坦尼克数据集（多方案）"""
    # 方案1：本地 CSV
    try:
        return pd.read_csv("titanic.csv")
    except FileNotFoundError:
        pass
    
    # 方案2：seaborn 内置数据集
    import seaborn as sns
    return sns.load_dataset("titanic")
```

---

## 二、数据清洗

### 2.1 探索性分析
```python
df = load_titanic_data()
print(f"数据形状：{df.shape}")  # (891, 15)
print(f"各列空值：\n{df.isnull().sum()}")
# Age: 177 个空值（约20%）
# Cabin: 687 个空值（约77%，基本放弃）
# Embarked: 2 个空值
```

### 2.2 清洗步骤
```python
def clean_data(df):
    """数据清洗"""
    df = df.copy()
    
    # 1. 填充年龄空值（用每个 Pclass + Sex 组合的中位数）
    df["Age"] = df.groupby(["Pclass", "Sex"])["Age"].transform(
        lambda x: x.fillna(x.median())
    )
    
    # 2. 填充 Embarked 空值（用众数）
    df["Embarked"].fillna(df["Embarked"].mode()[0], inplace=True)
    
    # 3. 删除 Cabin（空值过多，信息量少）
    if "Cabin" in df.columns:
        df.drop(columns=["Cabin"], inplace=True)
    
    # 4. 添加衍生特征
    df["FamilySize"] = df["SibSp"] + df["Parch"] + 1  # 家庭成员总数
    df["IsAlone"] = (df["FamilySize"] == 1).astype(int)
    
    # 5. 年龄分组
    bins = [0, 12, 18, 35, 55, 100]
    labels = ["儿童", "青少年", "青年", "中年", "老年"]
    df["AgeGroup"] = pd.cut(df["Age"], bins=bins, labels=labels, right=False)
    
    return df

df = clean_data(df)
```

---

## 三、多维度可视化分析

### 3.1 总体生还率
```python
survived_rate = df["Survived"].mean() * 100
print(f"总体生还率：{survived_rate:.1f}%")  # 38.4%
```

### 3.2 完整可视化代码
```python
def visualize_titanic(df):
    """6图综合可视化"""
    fig = plt.figure(figsize=(18, 14))
    gs = GridSpec(3, 3, figure=fig, hspace=0.4, wspace=0.3)
    
    # ──── 图1：性别 vs 生还率（条形图）────
    ax1 = fig.add_subplot(gs[0, 0])
    gender_survival = df.groupby("Sex")["Survived"].mean() * 100
    colors1 = ["#FF6B9D", "#4ECDC4"]
    bars1 = ax1.bar(gender_survival.index, gender_survival.values,
                    color=colors1, edgecolor="black", linewidth=0.8)
    for bar, val in zip(bars1, gender_survival.values):
        ax1.text(bar.get_x() + bar.get_width()/2, bar.get_height() + 1,
                 f"{val:.1f}%", ha="center", va="bottom", fontweight="bold")
    ax1.set_title("性别 vs 生还率", fontsize=12, fontweight="bold")
    ax1.set_ylabel("生还率 (%)")
    ax1.set_ylim(0, 100)
    ax1.grid(True, axis="y", alpha=0.3)
    
    # ──── 图2：船票等级 vs 生还率 ────
    ax2 = fig.add_subplot(gs[0, 1])
    class_survival = df.groupby("Pclass")["Survived"].mean() * 100
    colors2 = ["#FFD700", "#C0C0C0", "#CD7F32"]  # 金银铜
    bars2 = ax2.bar([f"{c}等舱" for c in class_survival.index],
                    class_survival.values,
                    color=colors2, edgecolor="black", linewidth=0.8)
    for bar, val in zip(bars2, class_survival.values):
        ax2.text(bar.get_x() + bar.get_width()/2, bar.get_height() + 1,
                 f"{val:.1f}%", ha="center", va="bottom", fontweight="bold")
    ax2.set_title("船票等级 vs 生还率", fontsize=12, fontweight="bold")
    ax2.set_ylabel("生还率 (%)")
    ax2.set_ylim(0, 100)
    ax2.grid(True, axis="y", alpha=0.3)
    
    # ──── 图3：性别×舱位交叉分析（热力图风格）────
    ax3 = fig.add_subplot(gs[0, 2])
    cross = df.groupby(["Pclass", "Sex"])["Survived"].mean() * 100
    cross_df = cross.unstack()  # Sex 展开为列
    im = ax3.imshow(cross_df.values, cmap="RdYlGn", vmin=0, vmax=100)
    ax3.set_xticks(range(len(cross_df.columns)))
    ax3.set_xticklabels(["女性", "男性"])
    ax3.set_yticks(range(len(cross_df.index)))
    ax3.set_yticklabels([f"{c}等舱" for c in cross_df.index])
    for i in range(len(cross_df.index)):
        for j in range(len(cross_df.columns)):
            ax3.text(j, i, f"{cross_df.values[i,j]:.1f}%",
                     ha="center", va="center", fontweight="bold", fontsize=12)
    plt.colorbar(im, ax=ax3, shrink=0.8)
    ax3.set_title("性别×舱位 生还率热力图", fontsize=12, fontweight="bold")
    
    # ──── 图4：年龄分布（遇难 vs 生还）────
    ax4 = fig.add_subplot(gs[1, 0:2])
    survived = df[df["Survived"] == 1]["Age"].dropna()
    deceased = df[df["Survived"] == 0]["Age"].dropna()
    ax4.hist(deceased, bins=30, alpha=0.6, color="#E74C3C", label="遇难", density=True)
    ax4.hist(survived, bins=30, alpha=0.6, color="#2ECC71", label="生还", density=True)
    ax4.set_title("年龄分布：遇难 vs 生还", fontsize=12, fontweight="bold")
    ax4.set_xlabel("年龄")
    ax4.set_ylabel("密度")
    ax4.legend()
    ax4.grid(True, alpha=0.3)
    
    # ──── 图5：年龄组 vs 生还率 ────
    ax5 = fig.add_subplot(gs[1, 2])
    if "AgeGroup" in df.columns:
        age_group_survival = df.groupby("AgeGroup", observed=True)["Survived"].mean() * 100
        bars5 = ax5.bar(age_group_survival.index, age_group_survival.values,
                        color=["#FF9FF3", "#54A0FF", "#5F27CD", "#00D2D3", "#FF6B6B"],
                        edgecolor="black", linewidth=0.8)
        for bar, val in zip(bars5, age_group_survival.values):
            ax5.text(bar.get_x() + bar.get_width()/2, bar.get_height() + 0.5,
                     f"{val:.1f}%", ha="center", va="bottom", fontsize=9, fontweight="bold")
    ax5.set_title("年龄组 vs 生还率", fontsize=12, fontweight="bold")
    ax5.set_ylabel("生还率 (%)")
    ax5.set_ylim(0, 100)
    ax5.grid(True, axis="y", alpha=0.3)
    
    # ──── 图6：登船港口 vs 生还率 ────
    ax6 = fig.add_subplot(gs[2, 0])
    embarked_map = {"S": "南安普顿", "C": "瑟堡", "Q": "昆士敦"}
    df["EmbarkName"] = df["Embarked"].map(embarked_map)
    emb_survival = df.groupby("EmbarkName")["Survived"].mean() * 100
    ax6.bar(emb_survival.index, emb_survival.values,
            color=["#FFEAA7", "#DFE6E9", "#FDCB6E"],
            edgecolor="black", linewidth=0.8)
    ax6.set_title("登船港口 vs 生还率", fontsize=12, fontweight="bold")
    ax6.set_ylabel("生还率 (%)")
    ax6.set_ylim(0, 100)
    ax6.grid(True, axis="y", alpha=0.3)
    
    # ──── 图7：票价分布（箱线图）────
    ax7 = fig.add_subplot(gs[2, 1:])
    survived_fare = [
        df[df["Survived"] == 0]["Fare"].dropna().values,
        df[df["Survived"] == 1]["Fare"].dropna().values
    ]
    bp = ax7.boxplot(survived_fare, labels=["遇难", "生还"],
                     patch_artist=True, notch=True)
    bp["boxes"][0].set_facecolor("#E74C3C")
    bp["boxes"][1].set_facecolor("#2ECC71")
    ax7.set_title("票价分布：遇难 vs 生还", fontsize=12, fontweight="bold")
    ax7.set_ylabel("票价（美元）")
    ax7.set_ylim(0, 300)
    ax7.grid(True, alpha=0.3)
    
    fig.suptitle("泰坦尼克号生还分析报告", fontsize=16, fontweight="bold", y=0.98)
    plt.savefig("titanic_analysis.png", dpi=150, bbox_inches="tight")
    plt.show()

visualize_titanic(df)
```

---

## 四、数据分析结论

### 📊 关键发现

1. **性别是最强因素**：女性生还率约 74%，男性仅约 19%
   - "妇女儿童优先"的疏散原则得到数据验证

2. **船票等级与生还强相关**：一等舱 63%、二等舱 47%、三等舱 24%
   - 一等舱更靠近救生艇甲板，且更先获得疏散指示

3. **儿童生还率最高**：约 58%，老年人最低（约 22%）

4. **瑟堡（C）乘客生还率最高**（约 55%）
   - 可能因为从瑟堡登船的多为一等舱富人

5. **票价越高，生还率越高**
   - 中位数：生还者约 26 美元，遇难者约 10 美元

---

## 五、知识点总结

### Pandas 高级操作

| 需求 | 代码 |
|------|------|
| 按组填充空值 | `df.groupby(cols)[col].transform(lambda x: x.fillna(x.median()))` |
| 交叉分析 | `df.groupby([col1, col2])[target].mean().unstack()` |
| 分箱分组 | `pd.cut(df["Age"], bins=bins, labels=labels)` |
| 添加衍生列 | `df["new"] = df["a"] + df["b"]` |

### Matplotlib 高级技巧

| 技巧 | 代码 |
|------|------|
| 复杂布局 | `GridSpec(rows, cols, hspace=..., wspace=...)` |
| 跨格子图 | `fig.add_subplot(gs[row, col1:col2])` |
| 热力图 | `ax.imshow(matrix, cmap="RdYlGn")` |
| 箱线图 | `ax.boxplot(data, patch_artist=True)` |
| 标注数值 | `ax.text(x, y, text, ha="center")` |

### 🌟 今日收获
- 交叉分析（`groupby` + `unstack`）是揭示多因素关系的利器
- `GridSpec` 可以创建跨格子的复杂图表布局
- 数据可视化不只是"好看"，更要能说明问题

---

**📝 作业提交记录**  
- 提交时间：2026年2月10日  
- GitHub Commit：`Day9：泰坦尼克号可视化分析完成`  

