# 📅 Day 3：Python 基础数据类型
**学习日期：** 2026年2月4日  
**任务来源：** QG2026 AI 组寒假训练营  

---

## 🎯 今日学习目标

| 序号 | 目标 | 完成状态 |
|------|------|----------|
| 1 | 了解 Python 编码规范（PEP 8） | ✅ |
| 2 | 掌握数字类型：int、float、bool、complex | ✅ |
| 3 | 掌握字符串（str）操作 | ✅ |
| 4 | 掌握列表（list）操作 | ✅ |
| 5 | 掌握字典（dict）操作 | ✅ |
| 6 | 掌握元组（tuple）和集合（set） | ✅ |
| 7 | 完成"情报解密"练习 | ✅ |

---

## 一、PEP 8 编码规范

PEP 8 是 Python 官方推荐的代码风格指南，遵循它能让代码更易读。

### 1.1 命名规范
| 类型 | 规范 | 示例 |
|------|------|------|
| 变量名 | 小写字母 + 下划线 | `user_name`, `total_count` |
| 函数名 | 小写字母 + 下划线 | `get_data()`, `parse_value()` |
| 类名 | 大驼峰（UpperCamelCase） | `MyClass`, `DataLoader` |
| 常量名 | 全大写 + 下划线 | `MAX_SIZE`, `PI` |
| 私有属性 | 前缀单下划线 | `_private_var` |
| 强私有属性 | 前缀双下划线 | `__very_private` |

### 1.2 格式规范
```python
# ✅ 正确：操作符两侧加空格
x = 1 + 2
result = a * b + c * d

# ✅ 正确：逗号后加空格
my_list = [1, 2, 3]
func(arg1, arg2)

# ✅ 正确：每行不超过 79 字符
# ✅ 正确：用 4 个空格缩进（不用 Tab）

# ❌ 错误写法
x=1+2
my_list=[1,2,3]
```

---

## 二、数字类型

### 2.1 int（整数）
```python
a = 10
b = -5
c = 0

# 不同进制
bin_num = 0b1010   # 二进制：10
oct_num = 0o12     # 八进制：10
hex_num = 0xa      # 十六进制：10

# 整除与取余
print(10 // 3)   # 3（整除）
print(10 % 3)    # 1（取余）
print(2 ** 10)   # 1024（幂运算）
```

### 2.2 float（浮点数）
```python
pi = 3.14159
e = 2.71828

# 科学计数法
large = 1.5e10   # 15000000000.0

# ⚠️ 浮点数精度问题
print(0.1 + 0.2)          # 0.30000000000000004
print(round(0.1 + 0.2, 1)) # 0.3（四舍五入）

# 使用 decimal 处理精确计算
from decimal import Decimal
result = Decimal('0.1') + Decimal('0.2')
print(result)  # 0.3
```

### 2.3 bool（布尔值）
```python
# bool 是 int 的子类！
print(True + True)   # 2
print(True * 5)      # 5

# 真值判断
print(bool(0))       # False
print(bool(""))      # False
print(bool([]))      # False
print(bool(None))    # False
print(bool(1))       # True
print(bool("hello")) # True
```

### 2.4 complex（复数）
```python
c = 3 + 4j
print(c.real)  # 3.0（实部）
print(c.imag)  # 4.0（虚部）
print(abs(c))  # 5.0（模）
```

---

## 三、字符串（str）

### 3.1 创建字符串
```python
s1 = 'Hello'
s2 = "World"
s3 = '''多行
字符串'''
s4 = f"Hello, {s1}!"   # f-string（推荐）
```

### 3.2 常用字符串方法
```python
s = "  Hello, Python World!  "

# 去除空格
s.strip()        # "Hello, Python World!"
s.lstrip()       # 去左侧空格
s.rstrip()       # 去右侧空格

# 大小写转换
s.upper()        # 全大写
s.lower()        # 全小写
s.title()        # 单词首字母大写
s.capitalize()   # 仅句首大写

# 查找与替换
s.find("Python")       # 返回索引，未找到返回 -1
s.index("Python")      # 返回索引，未找到抛出异常
s.replace("Python", "AI")  # 替换

# 分割与拼接
s.split(", ")          # 按分隔符分割，返回列表
", ".join(["a", "b"])  # 用分隔符拼接列表

# 判断
s.startswith("Hello")  # True/False
s.endswith("!")        # True/False
s.isdigit()            # 是否全为数字
s.isalpha()            # 是否全为字母
```

### 3.3 字符串切片
```python
s = "Hello, World!"
print(s[0:5])    # "Hello"
print(s[-6:-1])  # "World"
print(s[::-1])   # "!dlroW ,olleH"（反转）
print(s[::2])    # "Hlo ol!"（每隔一个取）
```

---

## 四、列表（list）

### 4.1 创建与访问
```python
fruits = ["apple", "banana", "cherry"]
print(fruits[0])    # "apple"
print(fruits[-1])   # "cherry"
print(fruits[1:3])  # ["banana", "cherry"]
```

### 4.2 常用列表操作
```python
nums = [3, 1, 4, 1, 5, 9, 2, 6]

# 增加元素
nums.append(7)           # 末尾添加
nums.insert(0, 0)        # 指定位置插入
nums.extend([8, 9, 10])  # 合并另一个列表

# 删除元素
nums.remove(1)   # 删除第一个值为 1 的元素
nums.pop()       # 删除并返回最后一个元素
nums.pop(2)      # 删除并返回索引2的元素
del nums[0]      # 删除索引0的元素

# 排序
nums.sort()              # 原地升序排序
nums.sort(reverse=True)  # 原地降序排序
sorted_nums = sorted(nums)  # 返回新列表，不修改原列表

# 其他
nums.reverse()   # 反转
nums.count(1)    # 统计元素出现次数
nums.index(5)    # 找元素的索引
len(nums)        # 列表长度
```

### 4.3 列表推导式
```python
# 基本形式：[表达式 for 变量 in 可迭代对象 if 条件]
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
matrix = [[i*j for j in range(1, 4)] for i in range(1, 4)]
```

---

## 五、字典（dict）

### 5.1 创建与访问
```python
student = {
    "name": "Alice",
    "age": 20,
    "scores": [90, 85, 92]
}

print(student["name"])          # "Alice"
print(student.get("grade", "N/A"))  # 不存在时返回默认值
```

### 5.2 常用字典操作
```python
# 增改
student["email"] = "alice@example.com"  # 添加新键
student["age"] = 21                      # 修改已有键

# 删除
del student["email"]
removed = student.pop("age")  # 删除并返回值

# 遍历
for key in student.keys():   print(key)
for val in student.values(): print(val)
for k, v in student.items(): print(f"{k}: {v}")

# 字典推导式
word_len = {word: len(word) for word in ["hello", "world", "python"]}
```

---

## 六、元组（tuple）与集合（set）

### 6.1 元组
```python
point = (3, 4)       # 不可修改！
x, y = point         # 解包

# 元组用途：函数多返回值、字典键（列表不能做键）
def min_max(lst):
    return min(lst), max(lst)

lo, hi = min_max([3, 1, 4, 1, 5, 9])
```

### 6.2 集合
```python
s = {1, 2, 3, 3, 2}  # 自动去重：{1, 2, 3}

# 集合运算
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}
print(a | b)   # 并集：{1, 2, 3, 4, 5, 6}
print(a & b)   # 交集：{3, 4}
print(a - b)   # 差集：{1, 2}
print(a ^ b)   # 对称差集：{1, 2, 5, 6}
```

---

## 七、今日作业：情报解密

### 任务描述
解析加密情报字符串，提取坐标、特工代号、物品清单等信息。

### 输入数据
```python
raw_intel = " Agent:007_Bond; Coords:(40,74); Items:gun,money,gun; Mission:2025-RESCUE-X "
```

### 解题思路
1. 去除首尾空格
2. 按 `"; "` 分割字段
3. 每个字段再按 `":"` 分割得到键值对
4. 特殊处理：坐标提取、物品去重、任务年份提取

### 完整代码实现
```python
"""Day 3 练习：情报解密  日期：2026年2月4日"""

raw_intel = " Agent:007_Bond; Coords:(40,74); Items:gun,money,gun; Mission:2025-RESCUE-X "

# 步骤1：去除首尾空格
intel = raw_intel.strip()

# 步骤2：分割字段
fields = intel.split("; ")

# 步骤3：解析字段
parsed = {}
for field in fields:
    key, value = field.split(":", 1)  # maxsplit=1 防止值中有冒号
    parsed[key.strip()] = value.strip()

# 步骤4：处理特殊字段
agent_id = parsed["Agent"]  # "007_Bond"

# 坐标处理：去掉括号，转为整数元组
coords_str = parsed["Coords"].strip("()")
x, y = map(int, coords_str.split(","))
coords = (x, y)

# 物品处理：去重并排序
items_raw = parsed["Items"].split(",")
items_unique = sorted(set(items_raw))  # 去重+排序

# 任务处理：提取年份
mission = parsed["Mission"]
year = int(mission.split("-")[0])

# 输出结果
print(f"特工代号：{agent_id}")
print(f"坐标：{coords}")
print(f"物品清单（去重后）：{items_unique}")
print(f"任务代号：{mission}")
print(f"任务年份：{year}")
```

### 输出结果
```
特工代号：007_Bond
坐标：(40, 74)
物品清单（去重后）：['gun', 'money']
任务代号：2025-RESCUE-X
任务年份：2025
```

---

## 八、知识点总结

### 数据类型速查表
| 类型 | 可变 | 有序 | 允许重复 | 示例 |
|------|------|------|----------|------|
| list | ✅ | ✅ | ✅ | `[1, 2, 3]` |
| tuple | ❌ | ✅ | ✅ | `(1, 2, 3)` |
| dict | ✅ | ✅(3.7+) | 键唯一 | `{"a": 1}` |
| set | ✅ | ❌ | ❌ | `{1, 2, 3}` |
| str | ❌ | ✅ | ✅ | `"hello"` |

### 🌟 今日收获
- Python 的数据类型设计非常灵活，理解可变/不可变是关键
- 字符串方法非常丰富，`split`/`join`/`strip` 是最常用的
- 推导式让代码更简洁，但不要过度使用影响可读性

### ❓ 常见陷阱
```python
# 陷阱1：列表作为默认参数
def append_item(item, lst=[]):  # ❌ 危险！
    lst.append(item)
    return lst

# 正确做法
def append_item(item, lst=None):  # ✅
    if lst is None:
        lst = []
    lst.append(item)
    return lst

# 陷阱2：浅拷贝 vs 深拷贝
a = [[1, 2], [3, 4]]
b = a.copy()    # 浅拷贝：内层列表仍共享
import copy
c = copy.deepcopy(a)  # 深拷贝：完全独立
```

---

**📝 作业提交记录**  
- 提交时间：2026年2月4日  
- GitHub Commit：`Day3：Python基础数据类型学习完成`  
