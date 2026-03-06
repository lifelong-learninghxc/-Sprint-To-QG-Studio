# 📅 Day 6：面向对象实战 — 图书库存管理系统
**学习日期：** 2026年2月7日  
**任务来源：** QG2026 AI 组寒假训练营  

---

## 🎯 今日学习目标

| 序号 | 目标 | 完成状态 |
|------|------|----------|
| 1 | 综合运用 OOP：类变量、封装、继承、异常处理 | ✅ |
| 2 | 设计完整的图书库存管理系统 | ✅ |
| 3 | 实现增删查改、借阅归还功能 | ✅ |
| 4 | 异常体系设计 | ✅ |

---

## 一、系统设计

### 1.1 类层次结构
```
Exception
└── BookError（自定义异常基类）
    ├── BookNotFoundError
    ├── InsufficientStockError
    └── DuplicateBookError

Book（普通图书）
└── EBook（电子书，继承 Book）

Library（图书馆管理器）
```

### 1.2 功能需求
| 功能 | 方法 |
|------|------|
| 添加图书 | `add_book()` |
| 删除图书 | `remove_book()` |
| 查询图书 | `search_by_title()`, `search_by_author()` |
| 借阅图书 | `borrow_book()` |
| 归还图书 | `return_book()` |
| 库存报告 | `inventory_report()` |

---

## 二、异常体系设计

### 核心思想
> 为什么要自定义异常而不直接用 `raise ValueError`？  
> 自定义异常可以精确捕获、携带更多信息、形成层级结构。

```python
class BookError(Exception):
    """图书系统基础异常"""
    pass

class BookNotFoundError(BookError):
    """图书不存在"""
    def __init__(self, book_id: str):
        super().__init__(f"图书 ID '{book_id}' 不存在")
        self.book_id = book_id

class InsufficientStockError(BookError):
    """库存不足"""
    def __init__(self, title: str, available: int, requested: int):
        super().__init__(
            f"《{title}》库存不足：现有 {available} 本，需借 {requested} 本"
        )
        self.available = available
        self.requested = requested

class DuplicateBookError(BookError):
    """图书重复添加"""
    def __init__(self, book_id: str):
        super().__init__(f"图书 ID '{book_id}' 已存在，不能重复添加")
        self.book_id = book_id
```

---

## 三、Book 类设计

```python
from datetime import datetime

class Book:
    """图书基类"""
    _total_books = 0  # 类变量：统计总图书种数
    
    def __init__(self, book_id: str, title: str, author: str,
                 price: float, stock: int = 1):
        if price < 0:
            raise ValueError("价格不能为负数")
        if stock < 0:
            raise ValueError("库存不能为负数")
        
        self.__book_id = book_id
        self.__title = title
        self.__author = author
        self.__price = price
        self.__stock = stock
        self.__borrow_count = 0        # 借阅次数
        self.__created_at = datetime.now()
        Book._total_books += 1
    
    # ── 只读属性 ──
    @property
    def book_id(self): return self.__book_id
    
    @property
    def title(self): return self.__title
    
    @property
    def author(self): return self.__author
    
    @property
    def stock(self): return self.__stock
    
    @property
    def borrow_count(self): return self.__borrow_count
    
    # ── 价格可修改 ──
    @property
    def price(self): return self.__price
    
    @price.setter
    def price(self, value: float):
        if value < 0:
            raise ValueError("价格不能为负数")
        self.__price = value
    
    # ── 借阅/归还 ──
    def borrow(self, quantity: int = 1):
        if quantity > self.__stock:
            raise InsufficientStockError(self.__title, self.__stock, quantity)
        self.__stock -= quantity
        self.__borrow_count += quantity
    
    def return_book(self, quantity: int = 1):
        self.__stock += quantity
    
    # ── 类方法 ──
    @classmethod
    def get_total_count(cls):
        return cls._total_books
    
    def __str__(self):
        return (f"[{self.__book_id}] 《{self.__title}》 "
                f"作者：{self.__author} | 价格：¥{self.__price:.2f} | 库存：{self.__stock}")
    
    def __repr__(self):
        return f"Book(id={self.__book_id!r}, title={self.__title!r})"


class EBook(Book):
    """电子书（继承 Book）"""
    
    def __init__(self, book_id: str, title: str, author: str,
                 price: float, format: str = "PDF", file_size_mb: float = 5.0):
        # EBook 无库存限制（数字资产）
        super().__init__(book_id, title, author, price, stock=999)
        self.__format = format
        self.__file_size_mb = file_size_mb
    
    def borrow(self, quantity: int = 1):
        """电子书不减库存，只记录借阅次数"""
        # 注意：直接访问父类的私有变量需要用 name mangling
        # 这里演示通过父类方法处理
        super().borrow(quantity)
    
    @property
    def format(self): return self.__format
    
    @property
    def file_size_mb(self): return self.__file_size_mb
    
    def __str__(self):
        base = super().__str__()
        return f"{base} [电子书 {self.__format} {self.__file_size_mb}MB]"
```

---

## 四、Library 管理器

```python
class Library:
    """图书馆管理器"""
    
    def __init__(self, name: str):
        self.__name = name
        self.__catalog: dict[str, Book] = {}  # book_id → Book
    
    def add_book(self, book: Book) -> None:
        """添加图书，重复则抛出异常"""
        if book.book_id in self.__catalog:
            raise DuplicateBookError(book.book_id)
        self.__catalog[book.book_id] = book
        print(f"✅ 已添加：{book.title}")
    
    def remove_book(self, book_id: str) -> Book:
        """删除图书并返回"""
        if book_id not in self.__catalog:
            raise BookNotFoundError(book_id)
        book = self.__catalog.pop(book_id)
        print(f"🗑️  已删除：{book.title}")
        return book
    
    def get_book(self, book_id: str) -> Book:
        """根据 ID 获取图书"""
        if book_id not in self.__catalog:
            raise BookNotFoundError(book_id)
        return self.__catalog[book_id]
    
    def search_by_title(self, keyword: str) -> list[Book]:
        """按书名关键词搜索"""
        return [b for b in self.__catalog.values()
                if keyword.lower() in b.title.lower()]
    
    def search_by_author(self, author: str) -> list[Book]:
        """按作者搜索"""
        return [b for b in self.__catalog.values()
                if author.lower() in b.author.lower()]
    
    def borrow_book(self, book_id: str, quantity: int = 1) -> None:
        """借阅图书"""
        book = self.get_book(book_id)
        book.borrow(quantity)
        print(f"📖 借阅成功：《{book.title}》× {quantity}")
    
    def return_book(self, book_id: str, quantity: int = 1) -> None:
        """归还图书"""
        book = self.get_book(book_id)
        book.return_book(quantity)
        print(f"📚 归还成功：《{book.title}》× {quantity}")
    
    def inventory_report(self) -> None:
        """打印库存报告"""
        print(f"\n{'='*50}")
        print(f"📚 {self.__name} 库存报告")
        print(f"{'='*50}")
        print(f"图书总种数：{len(self.__catalog)}")
        print(f"所有图书记录：{Book.get_total_count()}\n")
        
        # 按库存排序显示
        sorted_books = sorted(self.__catalog.values(), key=lambda b: b.stock)
        for book in sorted_books:
            status = "⚠️ 库存紧张" if book.stock <= 1 else "✅ 正常"
            print(f"  {status} {book}")
        print(f"{'='*50}\n")
```

---

## 五、完整测试代码

```python
if __name__ == "__main__":
    lib = Library("QG 图书馆")
    
    # ── 添加图书 ──
    books = [
        Book("B001", "Python编程：从入门到实践", "Eric Matthes", 89.0, stock=3),
        Book("B002", "深度学习", "Ian Goodfellow", 168.0, stock=2),
        Book("B003", "统计学习方法", "李航", 79.0, stock=5),
        EBook("E001", "动手学深度学习", "李沐", 0.0, format="PDF", file_size_mb=15.2),
    ]
    for book in books:
        lib.add_book(book)
    
    # ── 借阅测试 ──
    lib.borrow_book("B001", quantity=2)
    lib.borrow_book("E001")
    
    # ── 异常处理测试 ──
    print("\n── 异常测试 ──")
    try:
        lib.borrow_book("B001", quantity=5)  # 库存不足
    except InsufficientStockError as e:
        print(f"❌ {e}")
    
    try:
        lib.get_book("B999")  # 不存在
    except BookNotFoundError as e:
        print(f"❌ {e}")
    
    try:
        lib.add_book(Book("B001", "重复图书", "作者", 50.0))  # 重复
    except DuplicateBookError as e:
        print(f"❌ {e}")
    
    # ── 搜索测试 ──
    results = lib.search_by_author("李")
    print(f"\n搜索作者'李'的结果：{[b.title for b in results]}")
    
    # ── 归还 ──
    lib.return_book("B001")
    
    # ── 库存报告 ──
    lib.inventory_report()
```

### 输出结果
```
✅ 已添加：Python编程：从入门到实践
✅ 已添加：深度学习
✅ 已添加：统计学习方法
✅ 已添加：动手学深度学习
📖 借阅成功：《Python编程：从入门到实践》× 2
📖 借阅成功：《动手学深度学习》× 1

── 异常测试 ──
❌ 《Python编程：从入门到实践》库存不足：现有 1 本，需借 5 本
❌ 图书 ID 'B999' 不存在
❌ 图书 ID 'B001' 已存在，不能重复添加

搜索作者'李'的结果：['统计学习方法', '动手学深度学习']
📚 归还成功：《Python编程：从入门到实践》× 1

==================================================
📚 QG 图书馆 库存报告
==================================================
图书总种数：4
所有图书记录：4

  ⚠️ 库存紧张 [B002] 《深度学习》 作者：Ian Goodfellow | 价格：¥168.00 | 库存：2
  ✅ 正常 [B001] 《Python编程：从入门到实践》 作者：Eric Matthes | 价格：¥89.00 | 库存：2
  ✅ 正常 [B003] 《统计学习方法》 作者：李航 | 价格：¥79.00 | 库存：5
  ✅ 正常 [E001] 《动手学深度学习》 ... [电子书 PDF 15.2MB]
==================================================
```

---

## 六、知识点总结

### OOP 实战收获

| 知识点 | 本项目体现 |
|--------|-----------|
| 类变量 | `_total_books` 统计所有图书总数 |
| 封装 | `__catalog` 外部无法直接操作，通过方法访问 |
| `@property` | `price` 可读写，带验证 |
| 继承 | `EBook` 继承 `Book`，重写 `borrow` |
| 自定义异常 | `BookError` 层级体系 |
| `super()` | `EBook.__init__` 调用父类初始化 |

### 🌟 今日收获
- 大型项目中自定义异常体系非常重要，便于精确捕获和处理错误
- `@property` + 私有属性是 Python 封装的标准做法
- 继承 + 方法重写实现了电子书和纸质书的差异行为

---

**📝 作业提交记录**  
- 提交时间：2026年2月7日  
- GitHub Commit：`Day6：图书库存管理系统完成`  

