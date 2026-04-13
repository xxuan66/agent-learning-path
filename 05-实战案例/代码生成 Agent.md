# 代码生成 Agent

> 根据需求自动生成、测试、优化代码。

---

## 场景描述

开发者需要快速实现功能：
1. 描述需求，生成代码
2. 自动测试代码
3. 发现并修复问题
4. 优化代码质量

---

## 架构设计

```
用户需求 → 代码生成 Agent
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
 代码生成   代码执行   代码审查
   工具       工具      工具
    │         │         │
    └─────────┼─────────┘
              ▼
         最终代码
```

---

## 工具定义

代码生成 Agent 需要五个工具来实现"生成-执行-审查-修复-优化"的完整循环：

1. `generate_code`：根据自然语言需求生成代码
2. `execute_code`：在沙箱中执行代码并返回结果（支持 Python 和 JavaScript）
3. `review_code`：审查代码质量，发现 bug、性能问题和安全隐患
4. `fix_code`：根据错误信息修复代码
5. `optimize_code`：在功能正确的前提下优化代码可读性和性能

```python
from langchain.tools import tool
import subprocess
import tempfile

@tool
def generate_code(requirement: str, language: str = "python") -> str:
    """根据需求生成代码"""
    prompt = f"""请用 {language} 实现以下功能：
    
{requirement}

要求：
1. 代码可直接运行
2. 包含必要的注释
3. 处理边界情况
4. 遵循最佳实践

只返回代码，不要解释。"""
    
    return llm.call(prompt)

@tool
def execute_code(code: str, language: str = "python") -> str:
    """执行代码并返回结果"""
    with tempfile.NamedTemporaryFile(mode='w', suffix=f'.{language}', delete=False) as f:
        f.write(code)
        temp_file = f.name
    
    try:
        if language == "python":
            result = subprocess.run(
                ["python", temp_file],
                capture_output=True,
                text=True,
                timeout=30
            )
        elif language == "javascript":
            result = subprocess.run(
                ["node", temp_file],
                capture_output=True,
                text=True,
                timeout=30
            )
        
        if result.returncode == 0:
            return f"执行成功:\n{result.stdout}"
        else:
            return f"执行失败:\n{result.stderr}"
    except subprocess.TimeoutExpired:
        return "执行超时"
    finally:
        import os
        os.unlink(temp_file)

@tool
def review_code(code: str) -> str:
    """审查代码，发现问题和改进建议"""
    prompt = f"""请审查以下代码，找出：
1. 潜在的 bug
2. 性能问题
3. 安全隐患
4. 代码风格问题
5. 改进建议

代码：
```python
{code}
```

请用结构化格式输出审查结果。"""
    
    return llm.call(prompt)

@tool
def fix_code(code: str, error: str) -> str:
    """修复代码中的错误"""
    prompt = f"""以下代码有错误，请修复：

代码：
```python
{code}
```

错误信息：
{error}

请返回修复后的完整代码。"""
    
    return llm.call(prompt)

@tool
def optimize_code(code: str) -> str:
    """优化代码性能和可读性"""
    prompt = f"""请优化以下代码，提高：
1. 执行效率
2. 可读性
3. 可维护性

代码：
```python
{code}
```

请返回优化后的完整代码，并说明主要改进点。"""
    
    return llm.call(prompt)
```

---

## Agent 实现

```python
from langchain.agents import AgentExecutor, create_openai_agent
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(model="gpt-4", temperature=0)

tools = [generate_code, execute_code, review_code, fix_code, optimize_code]

agent = create_openai_agent(llm, tools, prompt)

executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True,
    max_iterations=15
)

# 使用示例
result = executor.invoke({
    "input": "用 Python 实现一个 LRU Cache，支持 get 和 put 操作，要求线程安全"
})
```

---

## 使用示例

### 输入
```
用 Python 实现一个 LRU Cache，要求：
1. 支持 get(key) 和 put(key, value) 操作
2. 容量固定，超过容量时淘汰最久未使用的数据
3. 时间复杂度 O(1)
4. 线程安全
```

### Agent 执行流程

```
1. 调用 generate_code("实现 LRU Cache...")
   → 生成初始代码

2. 调用 execute_code(code)
   → 执行成功，输出测试结果

3. 调用 review_code(code)
   → 发现：缺少类型注解，可改进

4. 调用 optimize_code(code)
   → 优化后的代码

5. 调用 execute_code(optimized_code)
   → 验证优化后的代码正常工作
```

### 输出
```python
from collections import OrderedDict
from threading import Lock
from typing import Any, Optional

class LRUCache:
    """线程安全的 LRU Cache 实现"""
    
    def __init__(self, capacity: int):
        if capacity <= 0:
            raise ValueError("容量必须大于0")
        
        self.capacity = capacity
        self.cache = OrderedDict()
        self.lock = Lock()
    
    def get(self, key: Any) -> Optional[Any]:
        """获取缓存值，如果不存在返回 None"""
        with self.lock:
            if key not in self.cache:
                return None
            # 移动到末尾（最近使用）
            self.cache.move_to_end(key)
            return self.cache[key]
    
    def put(self, key: Any, value: Any) -> None:
        """添加或更新缓存"""
        with self.lock:
            if key in self.cache:
                # 更新现有值
                self.cache.move_to_end(key)
            self.cache[key] = value
            
            # 超出容量，淘汰最久未使用的
            if len(self.cache) > self.capacity:
                self.cache.popitem(last=False)
    
    def __len__(self) -> int:
        return len(self.cache)
    
    def __contains__(self, key: Any) -> bool:
        return key in self.cache

# 测试代码
if __name__ == "__main__":
    cache = LRUCache(2)
    cache.put(1, "one")
    cache.put(2, "two")
    assert cache.get(1) == "one"
    cache.put(3, "three")  # 淘汰 key=2
    assert cache.get(2) is None
    print("所有测试通过!")
```

---

## 扩展方向

1. **多语言支持**：支持更多编程语言
2. **代码片段库**：复用常见代码模式
3. **测试用例生成**：自动生成单元测试
4. **文档生成**：自动生成 API 文档
5. **代码重构**：大规模重构建议

---

## 关键点

- **生成-执行-修复循环**：确保代码可运行
- **代码审查**：不只是能跑，还要质量好
- **安全隔离**：代码执行需要沙箱环境
- **版本管理**：保留代码修改历史

---

**作者：** xuan