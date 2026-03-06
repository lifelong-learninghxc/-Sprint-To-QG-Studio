# 📅 Day 7：NumPy 库学习
**学习日期：** 2026年2月8日  
**任务来源：** QG2026 AI 组寒假训练营  

---

## 🎯 今日学习目标

| 序号 | 目标 | 完成状态 |
|------|------|----------|
| 1 | 掌握 NumPy 数组的多种创建方式 | ✅ |
| 2 | 掌握 shape、dtype、ndim 等属性 | ✅ |
| 3 | 掌握矩阵运算：乘法、转置、逆、行列式 | ✅ |
| 4 | 掌握广播机制（Broadcasting） | ✅ |
| 5 | 完成 reshape 与 -1 用法练习 | ✅ |

---

## 一、为什么要用 NumPy？

| 操作 | Python 原生列表 | NumPy 数组 |
|------|-----------------|-----------|
| 向量加法 | `[a+b for a,b in zip(v1,v2)]` | `v1 + v2` |
| 速度 | 慢（纯 Python 循环） | 极快（C 语言底层，向量化） |
| 内存 | 浪费（对象开销） | 紧凑（连续内存块） |
| 功能 | 基础 | 丰富（线代、统计、随机等） |

```python
import numpy as np
```

---

## 二、数组创建

### 2.1 从 Python 列表创建
```python
a = np.array([1, 2, 3, 4, 5])            # 1D 数组
b = np.array([[1, 2, 3], [4, 5, 6]])      # 2D 数组
c = np.array([1, 2, 3], dtype=np.float64)  # 指定数据类型
```

### 2.2 特殊数组
```python
np.zeros((3, 4))        # 全零数组
np.ones((2, 3))         # 全一数组
np.eye(4)               # 4×4 单位矩阵
np.full((3, 3), 7)      # 全7数组
np.empty((2, 3))        # 未初始化（值不确定，但最快）

np.arange(0, 10, 2)     # [0, 2, 4, 6, 8]（步长为2）
np.linspace(0, 1, 11)   # [0.0, 0.1, ..., 1.0]（均匀11个点）
```

### 2.3 随机数组
```python
rng = np.random.default_rng(seed=42)  # 推荐：新版随机数生成器

rng.random((3, 4))          # [0, 1) 均匀分布
rng.integers(0, 10, (3, 3)) # 整数随机
rng.normal(0, 1, (3, 3))    # 标准正态分布
rng.choice([1,2,3,4,5], 3)  # 随机采样
```

---

## 三、数组属性

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])

print(arr.shape)    # (2, 3) → 2行3列
print(arr.ndim)     # 2 → 二维数组
print(arr.size)     # 6 → 元素总数
print(arr.dtype)    # int64
print(arr.itemsize) # 8 → 每个元素8字节
print(arr.nbytes)   # 48 → 总字节数
```

### 常见 dtype
| dtype | 说明 | 内存 |
|-------|------|------|
| `int32` | 32位整数 | 4字节 |
| `int64` | 64位整数 | 8字节 |
| `float32` | 单精度浮点（深度学习常用） | 4字节 |
| `float64` | 双精度浮点（默认） | 8字节 |
| `bool` | 布尔值 | 1字节 |
| `complex128` | 复数 | 16字节 |

---

## 四、reshape 与索引切片

### 4.1 reshape（最重要！）
```python
arr = np.arange(12)  # [0, 1, ..., 11]

# 基本 reshape
a = arr.reshape(3, 4)    # 3行4列
b = arr.reshape(4, 3)    # 4行3列
c = arr.reshape(2, 2, 3) # 三维

# ⭐ -1 的妙用：自动计算那个维度
d = arr.reshape(3, -1)   # 3行，列数自动算 → (3, 4)
e = arr.reshape(-1, 4)   # 列为4，行数自动算 → (3, 4)
f = arr.reshape(-1)      # 展平成1D

# flatten vs ravel
flat1 = a.flatten()  # 返回新数组（副本）
flat2 = a.ravel()    # 返回视图（修改会影响原数组，更快）
```

### 4.2 索引与切片
```python
arr = np.array([[1, 2, 3, 4],
                [5, 6, 7, 8],
                [9, 10, 11, 12]])

# 基本索引
arr[0, 1]     # 2（第0行第1列）
arr[-1, -1]   # 12（最后一行最后一列）

# 切片：[行切片, 列切片]
arr[0:2, 1:3]   # [[2,3],[6,7]]（前2行，第1-2列）
arr[:, 0]        # [1, 5, 9]（全部行，第0列）
arr[1:, ::2]     # [[5,7],[9,11]]（从第1行开始，列步长2）

# 布尔索引（⭐ 数据过滤的核心）
arr[arr > 5]     # 所有大于5的元素
arr[arr % 2 == 0]  # 所有偶数
```

### 4.3 高级索引
```python
# 花式索引
arr[[0, 2], [1, 3]]  # 取 (0,1) 和 (2,3) 两个点 → [2, 12]

# np.where（向量化 if-else）
result = np.where(arr > 6, arr, 0)  # 大于6保留，否则填0
```

---

## 五、矩阵运算

### 5.1 元素级运算（Broadcasting）
```python
a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])

a + b    # 元素相加
a * b    # 元素相乘（注意：不是矩阵乘法！）
a ** 2   # 每个元素平方
np.sqrt(a)  # 每个元素开方
```

### 5.2 矩阵乘法
```python
# ⭐ 矩阵乘法的三种写法
result1 = np.matmul(a, b)  # 推荐
result2 = a @ b             # Python 3.5+ 语法糖（最简洁）
result3 = np.dot(a, b)      # 老写法，2D时等价于 matmul

# 向量点积
v1 = np.array([1, 2, 3])
v2 = np.array([4, 5, 6])
dot = np.dot(v1, v2)   # 1*4+2*5+3*6 = 32
```

### 5.3 线性代数操作
```python
A = np.array([[1, 2], [3, 4]], dtype=float)

# 转置
A.T           # 或 np.transpose(A)

# 行列式
det = np.linalg.det(A)   # -2.0

# 逆矩阵
A_inv = np.linalg.inv(A)
print(A @ A_inv)  # 接近单位矩阵

# 特征值与特征向量
eigenvalues, eigenvectors = np.linalg.eig(A)

# 解线性方程组 Ax = b
b = np.array([1, 2])
x = np.linalg.solve(A, b)

# SVD 分解
U, s, Vt = np.linalg.svd(A)

# 矩阵的秩
rank = np.linalg.matrix_rank(A)
```

---

## 六、广播机制（Broadcasting）

### 规则
两个数组的形状从**尾部维度**开始对齐：
1. 如果维度相同，直接操作
2. 如果某维度为1，沿该维度扩展
3. 如果维度数不同，在前面补1

```python
# 示例1：标量与数组
arr = np.array([[1, 2, 3], [4, 5, 6]])
arr + 10   # 每个元素都加10

# 示例2：列向量与数组
col = np.array([[1], [2]])   # shape (2,1)
arr + col                    # shape (2,3) + (2,1) → (2,3)
# [[1+1, 2+1, 3+1],
#  [4+2, 5+2, 6+2]]

# 示例3：行向量与数组
row = np.array([10, 20, 30])  # shape (3,)
arr + row                     # shape (2,3) + (3,) → (2,3)
```

---

## 七、统计函数

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])

# 全局统计
arr.sum()        # 21
arr.mean()       # 3.5
arr.std()        # 标准差
arr.var()        # 方差
arr.min()        # 1
arr.max()        # 6
arr.argmin()     # 0（最小值的索引）
arr.argmax()     # 5

# 按轴统计（axis=0 按列，axis=1 按行）
arr.sum(axis=0)   # [5, 7, 9]（每列求和）
arr.sum(axis=1)   # [6, 15]（每行求和）
arr.mean(axis=1)  # [2.0, 5.0]（每行均值）

# 累积运算
arr.cumsum()      # 累积和
np.diff(arr)      # 差分
```

---

## 八、今日作业：reshape 与 -1 用法

### 任务代码
```python
"""Day 7 练习：NumPy 数组操作  日期：2026年2月8日"""
import numpy as np

# 任务1：创建 (3,4) 数组
arr = np.arange(12).reshape(3, 4)
print("原始数组：\n", arr)

# 任务2：数组属性
print(f"shape: {arr.shape}")   # (3, 4)
print(f"dtype: {arr.dtype}")   # int64
print(f"ndim: {arr.ndim}")     # 2
print(f"size: {arr.size}")     # 12

# 任务3：reshape
arr_43 = arr.reshape(4, 3)
print("(4,3) 形状：\n", arr_43)

arr_flat = arr.flatten()
print("展平后：", arr_flat)

# 任务4：-1 自动推断维度
a = arr.reshape(2, -1)    # 自动推断为 (2, 6)
b = arr.reshape(-1, 6)    # 自动推断为 (2, 6)
c = arr.reshape(-1)       # 完全展平 (12,)

print(f"reshape(2, -1): {a.shape}")    # (2, 6)
print(f"reshape(-1, 6): {b.shape}")    # (2, 6)
print(f"reshape(-1): {c.shape}")       # (12,)

# 任务5：矩阵运算
A = np.array([[1, 2], [3, 4]], dtype=float)
B = np.array([[5, 6], [7, 8]], dtype=float)

print("A @ B =\n", A @ B)
print("A.T =\n", A.T)
print("det(A) =", np.linalg.det(A))
print("inv(A) =\n", np.linalg.inv(A))
```

### 输出结果
```
原始数组：
 [[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]]
shape: (3, 4)  dtype: int64  ndim: 2  size: 12
reshape(2, -1): (2, 6)
reshape(-1, 6): (2, 6)
reshape(-1): (12,)
A @ B =
 [[19. 22.]
  [43. 50.]]
det(A) = -2.0
```

---

## 九、知识点总结

### NumPy 核心操作速查

| 操作 | 代码 | 说明 |
|------|------|------|
| 创建零数组 | `np.zeros((m,n))` | m×n 全零 |
| 单位矩阵 | `np.eye(n)` | n×n 单位阵 |
| 矩阵乘法 | `A @ B` | 矩阵积 |
| 元素乘法 | `A * B` | 哈达玛积 |
| 展平 | `arr.flatten()` | 返回副本 |
| 重塑 | `arr.reshape(-1, n)` | -1 自动推断 |
| 转置 | `A.T` | 交换行列 |
| 逆矩阵 | `np.linalg.inv(A)` | 需满秩 |

### 🌟 今日收获
- NumPy 的**广播机制**是其强大之处，理解后很多矩阵操作一行就能完成
- `reshape(-1, n)` 的 `-1` 是深度学习中最常见的操作（如将图像展平为向量）
- 矩阵乘法用 `@` 最简洁，注意区分 `*`（元素乘）和 `@`（矩阵乘）

### ⚠️ 常见陷阱
```python
# 陷阱1：视图 vs 副本
a = np.arange(6).reshape(2, 3)
b = a[0, :]      # 视图！修改 b 会影响 a
c = a[0, :].copy()  # 副本，安全

# 陷阱2：整数除法
a = np.array([1, 2, 3])
print(a / 2)     # [0.5, 1.0, 1.5]（float64）
print(a // 2)    # [0, 1, 1]（整数除法）
```

---

**📝 作业提交记录**  
- 提交时间：2026年2月8日  
- GitHub Commit：`Day7：NumPy库学习完成`  

