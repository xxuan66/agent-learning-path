# ReAct 模式

> Reasoning + Acting：边推理边行动，最基础也最常用的 Agent 模式。

---

## 核心思想

ReAct = Reasoning（推理） + Acting（行动）

Agent 交替进行：
1. **思考**：分析当前状态，决定下一步
2. **行动**：执行工具调用
3. **观察**：获取行动结果
4. **重复**：直到任务完成

---

## 工作流程

```
┌──────────────────────────────────────────────┐
│                                              │
│   ┌─────────┐                                │
│   │  用户   │                                │
│   │  输入   │                                │
│   └────┬────┘                                │
│        │                                     │
│        ▼                                     │
│   ┌─────────┐      ┌─────────┐              │
│   │  思考   │ ───► │  行动   │              │
│   │Thought │      │ Action  │              │
│   └────┬────┘      └────┬────┘              │
│        │                │                   │
│        │                ▼                   │
│        │           ┌─────────┐              │
│        │           │  观察   │              │
│        │           │Observation│            │
│        │           └────┬────┘              │
│        │                │                   │
│        └────────────────┘                   │
│              循环                            │
│                                             │
└──────────────────────────────────────────────┘
```

---

## 提示词模板

```
你是一个有用的助手。你可以使用以下工具：

{tool_descriptions}

请按照以下格式回答：

Question: 用户的问题
Thought: 我需要思考什么
Action: 工具名称
Action Input: 工具输入
Observation: 工具返回结果
... (可以重复多次)
Thought: 我现在知道答案了
Final Answer: 最终答案
```

---

## 代码示例

```python
class ReActAgent:
    def __init__(self, llm, tools, max_steps=10):
        self.llm = llm
        self.tools = {t.name: t for t in tools}
        self.max_steps = max_steps
    
    def run(self, question):
        prompt = f"Question: {question}\n"
        
        for step in range(self.max_steps):
            # 1. 思考
            response = self.llm.call(prompt + "Thought: ")
            prompt += f"Thought: {response}\n"
            
            # 2. 判断是否完成
            if "Final Answer:" in response:
                return response.split("Final Answer:")[1].strip()
            
            # 3. 提取行动
            action = self.parse_action(response)
            if action:
                # 4. 执行行动
                result = self.tools[action["name"]].run(action["input"])
                prompt += f"Action: {action['name']}\n"
                prompt += f"Action Input: {action['input']}\n"
                prompt += f"Observation: {result}\n"
        
        return "达到最大步数限制"
    
    def parse_action(self, text):
        # 解析 Action 和 Action Input
        # 简化实现
        if "Action:" in text:
            lines = text.split("\n")
            action = {}
            for line in lines:
                if line.startswith("Action:"):
                    action["name"] = line.split(":")[1].strip()
                elif line.startswith("Action Input:"):
                    action["input"] = line.split(":")[1].strip()
            return action if action.get("name") else None
        return None
```

---

## 优缺点

### 优点

- **简单直观**：容易理解和实现
- **通用性强**：适用于大多数任务
- **可解释**：思考过程可见
- **灵活**：可以使用任意工具

### 缺点

- **效率较低**：每步都需要 LLM 调用
- **可能循环**：可能陷入重复思考
- **依赖提示词**：效果受提示词质量影响
- **成本较高**：多次 LLM 调用

---

## 适用场景

- 问答系统
- 信息检索
- 简单任务执行
- 原型开发

## 不适用场景

- 复杂多步骤任务（用规划模式）
- 需要高质量输出（用反思模式）
- 需要并行处理（用其他模式）

---

## 改进方向

### 1. 添加记忆

```python
# 记录历史，避免重复
self.history.append({
    "thought": thought,
    "action": action,
    "observation": observation
})
```

### 2. 添加反思

```python
# 定期反思，调整策略
if step % 3 == 0:
    reflection = self.llm.call(f"回顾之前的步骤：{self.history}")
    prompt += f"Reflection: {reflection}\n"
```

### 3. 添加超时

```python
# 避免无限循环
if self.detect_loop():
    return "检测到循环，终止执行"
```

---

## 延伸阅读

- [ReAct 论文](https://arxiv.org/abs/2210.03629)
- [LangChain ReAct 实现](https://python.langchain.com/docs/modules/agents/agent_types/react)

---

**作者：** xuan