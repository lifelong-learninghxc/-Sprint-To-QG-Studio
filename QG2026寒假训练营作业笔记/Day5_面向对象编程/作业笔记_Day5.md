# 📅 Day 5：面向对象编程（OOP）
**学习日期：** 2026年2月6日  
**任务来源：** QG2026 AI 组寒假训练营  

---

## 🎯 今日学习目标

| 序号 | 目标 | 完成状态 |
|------|------|----------|
| 1 | 掌握类与实例化、类变量与实例变量 | ✅ |
| 2 | 掌握封装（私有属性/方法） | ✅ |
| 3 | 掌握继承与方法重写 | ✅ |
| 4 | 理解多态 | ✅ |
| 5 | 完成"图形计算器"练习 | ✅ |

---

## 一、类与对象基础

### 1.1 类的定义
```python
class Dog:
    # 类变量：所有实例共享
    species = "Canis familiaris"
    count = 0
    
    # 构造方法
    def __init__(self, name: str, age: int):
        # 实例变量：每个实例独有
        self.name = name
        self.age = age
        Dog.count += 1  # 修改类变量
    
    # 实例方法
    def bark(self):
        return f"{self.name} says: Woof!"
    
    # 类方法（操作类本身）
    @classmethod
    def get_count(cls):
        return f"共有 {cls.count} 只狗"
    
    # 静态方法（独立工具函数，不需要 self/cls）
    @staticmethod
    def is_adult(age: int) -> bool:
        return age >= 2
    
    # 字符串表示
    def __str__(self):
        return f"Dog({self.name}, {self.age}岁)"
    
    def __repr__(self):
        return f"Dog(name={self.name!r}, age={self.age!r})"
```

### 1.2 实例化与使用
```python
d1 = Dog("Buddy", 3)
d2 = Dog("Max", 1)

print(d1.bark())          # Buddy says: Woof!
print(Dog.get_count())    # 共有 2 只狗
print(Dog.is_adult(3))    # True（通过类调用）
print(d1.is_adult(1))     # False（通过实例也可以调用）
print(str(d1))            # Dog(Buddy, 3岁)
```

---

## 二、封装（Encapsulation）

### 2.1 访问控制
| 命名规则 | 含义 | 可访问性 |
|----------|------|----------|
| `name` | 公有属性 | 任何地方 |
| `_name` | 约定私有（Protected） | 建议外部不访问，但可以访问 |
| `__name` | 强私有（Name Mangling） | 外部无法直接访问 |

### 2.2 Property 装饰器（推荐使用）
```python
class BankAccount:
    def __init__(self, initial_balance: float):
        self.__balance = initial_balance  # 强私有
    
    @property
    def balance(self):
        """只读属性：外部只能读，不能写"""
        return self.__balance
    
    @balance.setter
    def balance(self, amount: float):
        """写入时做验证"""
        if amount < 0:
            raise ValueError("余额不能为负数")
        self.__balance = amount
    
    def deposit(self, amount: float):
        if amount <= 0:
            raise ValueError("存款金额必须为正数")
        self.__balance += amount
    
    def withdraw(self, amount: float):
        if amount > self.__balance:
            raise ValueError("余额不足")
        self.__balance -= amount

# 使用
acc = BankAccount(1000)
print(acc.balance)   # 1000（通过 @property 访问）
acc.deposit(500)
print(acc.balance)   # 1500
# acc.__balance      # ❌ AttributeError
# acc._BankAccount__balance  # ✅ 可以，但不推荐
```

---

## 三、继承（Inheritance）

### 3.1 单继承
```python
class Animal:
    def __init__(self, name: str):
        self.name = name
    
    def speak(self) -> str:
        raise NotImplementedError("子类必须实现 speak 方法")
    
    def __str__(self):
        return f"{self.__class__.__name__}({self.name})"

class Dog(Animal):
    def speak(self) -> str:
        return f"{self.name}: 汪汪！"

class Cat(Animal):
    def speak(self) -> str:
        return f"{self.name}: 喵喵！"

# 调用父类方法
class GuideDog(Dog):
    def __init__(self, name: str, owner: str):
        super().__init__(name)  # 调用父类 __init__
        self.owner = owner
    
    def speak(self) -> str:
        base = super().speak()  # 调用父类方法
        return f"{base}（导盲犬，主人：{self.owner}）"
```

### 3.2 多继承与 MRO
```python
class A:
    def hello(self): return "A"

class B(A):
    def hello(self): return "B"

class C(A):
    def hello(self): return "C"

class D(B, C):  # 多继承
    pass

d = D()
print(d.hello())  # "B"（MRO: D → B → C → A）
print(D.__mro__)  # 查看方法解析顺序
```

---

## 四、多态（Polymorphism）

```python
# 同一接口，不同行为
animals = [Dog("Buddy"), Cat("Whiskers"), GuideDog("Rex", "Alice")]

for animal in animals:
    print(animal.speak())  # 各自调用自己的 speak 方法

# 鸭子类型：Python 不要求严格继承，只要有相同方法即可
class Robot:
    def speak(self):
        return "我是机器人，哔哔哔！"

for entity in [Dog("Max"), Robot()]:
    print(entity.speak())  # 都能工作！
```

---

## 五、特殊方法（魔法方法）

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __add__(self, other):      # v1 + v2
        return Vector(self.x + other.x, self.y + other.y)
    
    def __sub__(self, other):      # v1 - v2
        return Vector(self.x - other.x, self.y - other.y)
    
    def __mul__(self, scalar):     # v * 3
        return Vector(self.x * scalar, self.y * scalar)
    
    def __len__(self):             # len(v)
        return int((self.x**2 + self.y**2) ** 0.5)
    
    def __eq__(self, other):       # v1 == v2
        return self.x == other.x and self.y == other.y
    
    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

v1 = Vector(1, 2)
v2 = Vector(3, 4)
print(v1 + v2)   # Vector(4, 6)
print(v1 * 3)    # Vector(3, 6)
```

---

## 六、今日作业：图形计算器

### 任务描述
实现一个面向对象的图形计算器，支持圆形、矩形、三角形，具有封装、继承、多态特性。

### 设计思路
```
Shape（抽象基类）
├── Circle（圆形）
├── Rectangle（矩形）
└── Triangle（三角形）
```

### 完整代码实现
```python
"""Day 5 练习：图形计算器  日期：2026年2月6日"""

import math
from abc import ABC, abstractmethod

class Shape(ABC):
    """抽象基类：所有图形的父类"""
    _count = 0  # 类变量：统计创建的图形总数
    
    def __init__(self, color: str = "白色"):
        self._color = color
        self.__area = None   # 强私有，懒计算缓存
        Shape._count += 1
    
    @abstractmethod
    def _calc_area(self) -> float:
        """子类必须实现的面积计算"""
        pass
    
    @abstractmethod
    def _calc_perimeter(self) -> float:
        """子类必须实现的周长计算"""
        pass
    
    def get_area(self) -> float:
        """懒计算：只在第一次调用时计算"""
        if self.__area is None:
            self.__area = self._calc_area()
        return self.__area
    
    def get_perimeter(self) -> float:
        return self._calc_perimeter()
    
    @property
    def color(self):
        return self._color
    
    @color.setter
    def color(self, value: str):
        self._color = value
        self.__area = None  # 颜色不影响面积，但这里演示 setter 的用法
    
    @classmethod
    def total_count(cls):
        return f"共创建了 {cls._count} 个图形"
    
    def describe(self) -> str:
        return (f"[{self.__class__.__name__}] 颜色：{self._color} | "
                f"面积：{self.get_area():.2f} | 周长：{self.get_perimeter():.2f}")


class Circle(Shape):
    def __init__(self, radius: float, color: str = "红色"):
        if radius <= 0:
            raise ValueError(f"半径必须为正数，但得到 {radius}")
        super().__init__(color)
        self.__radius = radius
    
    def _calc_area(self) -> float:
        return math.pi * self.__radius ** 2
    
    def _calc_perimeter(self) -> float:
        return 2 * math.pi * self.__radius
    
    @property
    def radius(self):
        return self.__radius


class Rectangle(Shape):
    def __init__(self, width: float, height: float, color: str = "蓝色"):
        if width <= 0 or height <= 0:
            raise ValueError("宽度和高度必须为正数")
        super().__init__(color)
        self.__width = width
        self.__height = height
    
    def _calc_area(self) -> float:
        return self.__width * self.__height
    
    def _calc_perimeter(self) -> float:
        return 2 * (self.__width + self.__height)
    
    def is_square(self) -> bool:
        return self.__width == self.__height


class Triangle(Shape):
    def __init__(self, a: float, b: float, c: float, color: str = "绿色"):
        if a + b <= c or a + c <= b or b + c <= a:
            raise ValueError("三边不能构成三角形")
        super().__init__(color)
        self.__a = a
        self.__b = b
        self.__c = c
    
    def _calc_area(self) -> float:
        # 海伦公式
        s = (self.__a + self.__b + self.__c) / 2
        return math.sqrt(s * (s - self.__a) * (s - self.__b) * (s - self.__c))
    
    def _calc_perimeter(self) -> float:
        return self.__a + self.__b + self.__c


# ── 测试 ──
if __name__ == "__main__":
    shapes = [
        Circle(5),
        Rectangle(4, 6),
        Rectangle(5, 5),
        Triangle(3, 4, 5),
    ]
    
    for shape in shapes:
        print(shape.describe())
    
    print(Shape.total_count())
    
    # 多态演示
    print("\n面积排行榜：")
    sorted_shapes = sorted(shapes, key=lambda s: s.get_area(), reverse=True)
    for rank, shape in enumerate(sorted_shapes, 1):
        print(f"  {rank}. {shape.__class__.__name__}: {shape.get_area():.2f}")
```

### 输出结果
```
[Circle] 颜色：红色 | 面积：78.54 | 周长：31.42
[Rectangle] 颜色：蓝色 | 面积：24.00 | 周长：20.00
[Rectangle] 颜色：蓝色 | 面积：25.00 | 周长：20.00
[Triangle] 颜色：绿色 | 面积：6.00 | 周长：12.00
共创建了 4 个图形

面积排行榜：
  1. Circle: 78.54
  2. Rectangle: 25.00
  3. Rectangle: 24.00
  4. Triangle: 6.00
```

---

## 七、知识点总结

### OOP 四大特性速记

| 特性 | 关键字/技术 | 目的 |
|------|-------------|------|
| **封装** | `__private`, `@property` | 隐藏实现细节，保护数据 |
| **继承** | `class Child(Parent)` | 代码复用，建立层级关系 |
| **多态** | 方法重写 | 同一接口，不同实现 |
| **抽象** | `ABC`, `@abstractmethod` | 规范接口，强制子类实现 |

### 🌟 今日收获
- `@property` 是 Python 封装的最佳实践，比直接暴露属性更安全
- `ABC + @abstractmethod` 强制子类实现指定方法，是大型项目的常见模式
- 懒计算（Lazy Evaluation）是一种优化技巧，避免重复计算

### 💡 设计原则（SOLID 初接触）
- **S**ingle Responsibility：每个类只做一件事
- **O**pen/Closed：对扩展开放，对修改关闭（用继承扩展，不改父类）
- **L**iskov Substitution：子类可以替换父类使用

---

**📝 作业提交记录**  
- 提交时间：2026年2月6日  
- GitHub Commit：`Day5：面向对象编程学习完成`  

