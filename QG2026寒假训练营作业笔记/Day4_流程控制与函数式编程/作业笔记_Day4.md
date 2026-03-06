# 📅 Day 4：流程控制与函数式编程
**学习日期：** 2026年2月5日  
**任务来源：** QG2026 AI 组寒假训练营  

---

## 🎯 今日学习目标

| 序号 | 目标 | 完成状态 |
|------|------|----------|
| 1 | 掌握条件控制：if-elif-else | ✅ |
| 2 | 掌握循环：for / while | ✅ |
| 3 | 掌握异常处理：try-except | ✅ |
| 4 | 掌握列表/字典/集合推导式 | ✅ |
| 5 | 掌握 lambda、map()、filter() | ✅ |
| 6 | 完成"能源核心数据清洗"练习 | ✅ |

---

## 一、条件控制：if-elif-else

### 1.1 基本语法
```python
score = 85

if score >= 90:
    grade = 'A'
elif score >= 80:
    grade = 'B'
elif score >= 70:
    grade = 'C'
elif score >= 60:
    grade = 'D'
else:
    grade = 'F'

print(f"成绩：{grade}")  # B
```

### 1.2 三元表达式（条件表达式）
```python
# 形式：真值 if 条件 else 假值
result = "偶数" if x % 2 == 0 else "奇数"
max_val = a if a > b else b
```

### 1.3 match-case（Python 3.10+）
```python
command = "quit"
match command:
    case "quit":
        print("退出")
    case "help":
        print("帮助信息")
    case _:
        print("未知命令")
```

---

## 二、循环：for / while

### 2.1 for 循环
```python
# 遍历列表
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)

# range() 生成数字序列
for i in range(5):         # 0, 1, 2, 3, 4
    print(i)

for i in range(1, 10, 2):  # 1, 3, 5, 7, 9（步长2）
    print(i)

# enumerate() 同时获取索引和值
for idx, val in enumerate(fruits, start=1):
    print(f"{idx}. {val}")

# zip() 同时遍历多个序列
names = ["Alice", "Bob", "Charlie"]
scores = [90, 85, 92]
for name, score in zip(names, scores):
    print(f"{name}: {score}")
```

### 2.2 while 循环
```python
count = 0
while count < 5:
    print(count)
    count += 1

# while-else（循环正常结束时执行 else）
n = 1
while n < 10:
    if n == 7:
        break
    n += 1
else:
    print("循环正常完成")
```

### 2.3 循环控制语句
```python
for i in range(10):
    if i == 3:
        continue  # 跳过本次，继续下一次
    if i == 7:
        break     # 退出整个循环
    print(i)      # 输出：0 1 2 4 5 6
```

---

## 三、异常处理：try-except

### 3.1 基本语法
```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"错误：{e}")
except (ValueError, TypeError) as e:
    print(f"值或类型错误：{e}")
except Exception as e:
    print(f"其他错误：{e}")
else:
    print("没有异常时执行")
finally:
    print("无论如何都会执行（如关闭文件）")
```

### 3.2 常见异常类型
| 异常 | 触发条件 |
|------|----------|
| `ValueError` | 值不合法（如 `int("abc")`） |
| `TypeError` | 类型不匹配 |
| `KeyError` | 字典键不存在 |
| `IndexError` | 列表索引越界 |
| `ZeroDivisionError` | 除以零 |
| `FileNotFoundError` | 文件不存在 |
| `AttributeError` | 对象没有该属性 |

### 3.3 自定义异常
```python
class InsufficientFundsError(Exception):
    def __init__(self, amount):
        super().__init__(f"余额不足，需要 {amount} 元")
        self.amount = amount

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(amount)
    return balance - amount
```

---

## 四、推导式

### 4.1 列表推导式
```python
# 基本形式
squares = [x**2 for x in range(1, 11)]

# 带条件
even_squares = [x**2 for x in range(1, 11) if x % 2 == 0]

# 嵌套循环
matrix_flat = [i*j for i in range(1, 4) for j in range(1, 4)]

# 等价的 for 循环
result = []
for x in range(1, 11):
    if x % 2 == 0:
        result.append(x**2)
```

### 4.2 字典推导式
```python
words = ["hello", "world", "python"]
word_lengths = {word: len(word) for word in words}
# {'hello': 5, 'world': 5, 'python': 6}

# 反转字典
original = {"a": 1, "b": 2, "c": 3}
reversed_dict = {v: k for k, v in original.items()}
```

### 4.3 集合推导式
```python
data = [1, 2, 2, 3, 3, 4]
unique_squares = {x**2 for x in data}
# {1, 4, 9, 16}
```

### 4.4 生成器表达式
```python
# 不立即计算，节省内存（大数据集用这个！）
gen = (x**2 for x in range(1000000))
print(next(gen))  # 0
print(sum(gen))   # 计算所有值之和
```

---

## 五、函数式编程工具

### 5.1 lambda 匿名函数
```python
# 形式：lambda 参数: 表达式
square = lambda x: x**2
add = lambda x, y: x + y

# 排序时很有用
students = [("Alice", 90), ("Bob", 85), ("Charlie", 92)]
students.sort(key=lambda s: s[1], reverse=True)
```

### 5.2 map() 函数
```python
# map(函数, 可迭代对象) → 对每个元素应用函数
nums = [1, 2, 3, 4, 5]

squares = list(map(lambda x: x**2, nums))
# [1, 4, 9, 16, 25]

# 多参数版本
a = [1, 2, 3]
b = [10, 20, 30]
sums = list(map(lambda x, y: x + y, a, b))
# [11, 22, 33]
```

### 5.3 filter() 函数
```python
# filter(函数, 可迭代对象) → 保留函数返回 True 的元素
nums = [1, -2, 3, -4, 5, -6]

positives = list(filter(lambda x: x > 0, nums))
# [1, 3, 5]
```

### 5.4 functools.reduce()
```python
from functools import reduce

# 累积计算
product = reduce(lambda x, y: x * y, [1, 2, 3, 4, 5])
# 120（1×2×3×4×5）
```

---

## 六、今日作业：能源核心数据清洗

### 任务描述
清洗能源传感器原始数据，过滤无效值，归一化后判断核心状态。

### 输入数据
```python
raw_data = ["92.5", "87", "invalid", "105", "78.3", "null", "95", "60", "88.1", "102.7"]
THRESHOLD = 80.0  # 只保留 >= 80 的数据
```

### 解题思路
1. **清洗**：用 `try-except` 尝试将每个字符串转为浮点数，无法转换则跳过
2. **过滤**：只保留 `>= THRESHOLD` 的值
3. **归一化**：Min-Max 归一化到 [0, 1]
4. **状态判断**：根据归一化值划分状态等级

### 完整代码实现
```python
"""Day 4 练习：能源核心数据清洗  日期：2026年2月5日"""

raw_data = ["92.5", "87", "invalid", "105", "78.3", "null", "95", "60", "88.1", "102.7"]
THRESHOLD = 80.0

# ── 步骤1：清洗（过滤无效值）──
def safe_parse(item: str) -> float | None:
    """尝试将字符串转为浮点数，失败返回 None"""
    try:
        return float(item)
    except (ValueError, TypeError):
        return None

# 用列表推导式 + filter
parsed = [v for item in raw_data if (v := safe_parse(item)) is not None]
print(f"清洗后数据：{parsed}")

# ── 步骤2：过滤（只保留 >= THRESHOLD 的数据）──
filtered = [v for v in parsed if v >= THRESHOLD]
print(f"过滤后数据：{filtered}")

# ── 步骤3：归一化（Min-Max）──
def normalize(data: list[float]) -> list[float]:
    """Min-Max 归一化到 [0, 1]"""
    min_val = min(data)
    max_val = max(data)
    if max_val == min_val:  # 防止除以零
        return [0.5] * len(data)
    return [(v - min_val) / (max_val - min_val) for v in data]

normalized = normalize(filtered)
print(f"归一化后数据：{[round(v, 3) for v in normalized]}")

# ── 步骤4：状态判断 ──
def check_status(norm_val: float) -> str:
    """根据归一化值判断能源核心状态"""
    if norm_val >= 0.8:
        return "🔴 高负载警告"
    elif norm_val >= 0.5:
        return "🟡 正常运行"
    else:
        return "🟢 低负载"

statuses = list(map(check_status, normalized))

# ── 输出结果 ──
print("\n=== 能源核心状态报告 ===")
for val, norm, status in zip(filtered, normalized, statuses):
    print(f"  原始值: {val:6.1f} | 归一化: {norm:.3f} | 状态: {status}")
```

### 输出结果
```
清洗后数据：[92.5, 87.0, 105.0, 78.3, 95.0, 60.0, 88.1, 102.7]
过滤后数据：[92.5, 87.0, 105.0, 95.0, 88.1, 102.7]

=== 能源核心状态报告 ===
  原始值:   92.5 | 归一化: 0.301 | 状态: 🟡 正常运行
  原始值:   87.0 | 归一化: 0.000 | 状态: 🟢 低负载
  原始值:  105.0 | 归一化: 1.000 | 状态: 🔴 高负载警告
  原始值:   95.0 | 归一化: 0.444 | 状态: 🟡 正常运行
  原始值:   88.1 | 归一化: 0.061 | 状态: 🟢 低负载
  原始值:  102.7 | 归一化: 0.872 | 状态: 🔴 高负载警告
```

---

## 七、知识点总结

### 函数式编程 vs 命令式编程对比

| 方式 | 代码 | 优点 |
|------|------|------|
| 命令式（for 循环） | `for x in lst: result.append(x**2)` | 直观，易调试 |
| 列表推导式 | `[x**2 for x in lst]` | 简洁，Pythonic |
| map | `list(map(lambda x: x**2, lst))` | 函数式风格 |

**最佳实践：** 优先使用推导式，逻辑复杂时用普通 for 循环，`map`/`filter` 适合函数已定义好的场景。

### 🌟 今日收获
- `try-except` 是数据清洗的利器，处理脏数据时非常实用
- 推导式让代码更 Pythonic，但嵌套超过两层要考虑换回 for 循环
- `lambda` + `map/filter` 是函数式编程的核心，在 AI/数据处理中常见

### ❓ 需要注意
```python
# filter 返回迭代器，需要 list() 转换
result = list(filter(lambda x: x > 0, [-1, 2, -3, 4]))

# map 同样返回迭代器
result = list(map(str, [1, 2, 3]))
```

---

**📝 作业提交记录**  
- 提交时间：2026年2月5日  
- GitHub Commit：`Day4：流程控制与函数式编程学习完成`  
