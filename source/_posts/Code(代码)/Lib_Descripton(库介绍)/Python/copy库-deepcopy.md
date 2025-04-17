---
title: copy库-deepcopy函数介绍
tags:
  - Python
date: 2025-04-14
---
Python中的`deepcopy`是`copy`模块提供的一个函数，用于创建对象的**深拷贝**（deep copy），而不是一个独立的库。以下是详细介绍：

---

### **1. 深拷贝的作用**
- **核心功能**：递归地复制对象及其所有子对象，生成一个完全独立的副本。修改副本不会影响原始对象。
- **与浅拷贝的区别**：
  - **浅拷贝**（`copy.copy()`）：仅复制对象本身，内部的子对象仍为原对象的引用。例如，列表的浅拷贝副本中的嵌套列表仍与原对象共享。
  - **深拷贝**（`copy.deepcopy()`）：递归复制所有层级的子对象，确保副本与原对象完全独立。

**示例**：
```python
import copy
original = [[1, 2], [3, 4]]
shallow = copy.copy(original)
deep = copy.deepcopy(original)

# 修改浅拷贝的嵌套列表
shallow = 99
print(original)  # [[99, 2], [3, 4]]（原对象被影响）

# 修改深拷贝的嵌套列表
deep = 100
print(original)  # [[99, 2], [3, 4]]（原对象不受影响）
```

---

### **2. 适用场景**
- **嵌套数据结构**：如包含列表的字典、多层嵌套的列表等，需完全独立副本时。
- **避免副作用**：在函数中修改传入的可变对象时，可用深拷贝保留原始数据。
- **配置管理**：复制配置模板并修改，不影响原始配置。

---

### **3. 注意事项**
- **循环引用**：深拷贝能处理递归对象（如对象A引用对象B，B又引用A），通过`memo`字典记录已复制的对象，避免无限循环。
- **不可复制的对象**：如模块、线程锁、文件句柄等，深拷贝会直接返回原对象。
- **性能开销**：深拷贝递归复制所有子对象，时间和内存成本较高，需谨慎使用。

---

### **4. 使用方法**
```python
import copy

original = {"key": [1, 2, 3]}
copied = copy.deepcopy(original)  # 创建深拷贝
```

---

### **5. 自定义类的深拷贝**
若类需要自定义深拷贝逻辑，可定义`__deepcopy__`方法：
```python
class MyClass:
    def __init__(self, data):
        self.data = data
    
    def __deepcopy__(self, memo):
        # 手动处理深拷贝逻辑
        new_data = copy.deepcopy(self.data, memo)
        return MyClass(new_data)
```

---

### **总结**
- **深拷贝**适用于需要完全独立副本的场景，确保数据修改的隔离性。
- **浅拷贝**适用于简单对象或无需隔离子对象的情况。
- 官方文档参考：[Python copy模块](https://docs.python.org/zh-cn/3/library/copy.html)。

若有更复杂的复制需求（如替换对象字段），可探索`copy.replace()`（Python 3.13+）。