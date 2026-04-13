# Agent 核心能力

Agent 的核心能力不是几个孤立模块的拼装，而是决定它能不能从“会回答”走向“会完成任务”的关键条件。

如果只记一句话，可以这样理解：

> Agent 的核心能力，本质上是在目标驱动下，把理解、规划、执行、记忆和纠偏串成一个闭环。

## 先建立一个整体理解

很多人提到 Agent，会直接想到工具调用。但真正决定系统上限的，往往不是某一个工具，而是这些能力能不能协同工作：

- 规划：知道下一步做什么
- 记忆：知道应该保留什么信息
- 工具使用：知道什么时候借外部能力
- 反思：知道哪里做错了以及怎么调整

## 1️⃣ 规划能力（Planning）

规划是 Agent 将复杂目标分解为可执行步骤的能力。

### 任务分解

下面这段代码用"规划日本 7 日游"来演示任务分解。核心思想是：Agent 接到一个大目标后，把它拆成若干个可执行的子任务，每个子任务都足够具体、可以独立完成。

```python
# 示例：旅行规划任务分解
task = "规划一次日本 7 日游"

# 分解为子任务
subtasks = [
    "查询日本签证要求",
    "搜索东京、京都、大阪景点",
    "比较航班价格和时间",
    "预订酒店住宿",
    "制定每日行程",
    "准备行李清单",
    "购买旅游保险"
]
```

### 规划技术

规划不只是简单地列步骤，有三种主流的规划方法，复杂度递增：

| 技术 | 描述 | 通俗理解 | 适用场景 |
|------|------|----------|----------|
| Chain of Thought (CoT) | 逐步推理 | 像做数学题一样，一步步写出思考过程 | 数学问题、逻辑推理 |
| Tree of Thoughts (ToT) | 多路径探索 | 像下棋一样，同时考虑多种走法，选最优的 | 创意写作、策略决策 |
| LLM+P | 结合经典规划器 | LLM 负责理解意图，传统规划器负责精确计算步骤 | 机器人控制、长程规划 |

### 一个现实判断

不是所有 Agent 都需要复杂规划。只有当任务本身是多步、依赖外部信息、并且中间可能失败时，规划能力才真正变得重要。

### 代码示例

下面的代码演示了 CoT（逐步推理）的核心原理：不是直接让模型给答案，而是先让它写出思考步骤。实际使用中，加上"让我们一步一步思考"这样的引导语，准确率能提升 20-30%。

```python
from langchain.prompts import PromptTemplate

# CoT 提示模板
cot_prompt = PromptTemplate.from_template("""
请逐步思考以下问题：

问题：{question}

思考步骤：
1. 首先，我需要理解问题的核心...
2. 然后，我应该分析相关因素...
3. 接下来，我可以...
4. 最后，得出结论...

答案：
""")
```

## 2️⃣ 记忆能力（Memory）

记忆使 Agent 能够存储和检索信息，实现持续学习和上下文理解。

### 记忆类型

```
┌─────────────────────────────────────────┐
│            Agent 记忆系统                │
├─────────────────────────────────────────┤
│                                         │
│  短期记忆 (Short-term)                  │
│  ├── 当前对话上下文                      │
│  ├── 最近的操作历史                      │
│  └── 临时变量和状态                      │
│                                         │
│  长期记忆 (Long-term)                   │
│  ├── 向量数据库 (Vector DB)              │
│  ├── 用户偏好和习惯                      │
│  ├── 历史经验和教训                      │
│  └── 知识库和文档                        │
│                                         │
└─────────────────────────────────────────┘
```

### 一个现实判断

记忆不是“存得越多越好”。真正重要的是：

- 存什么
- 什么时候取
- 取出来后怎么影响当前决策

### 实现示例

下面的代码展示了短期记忆和长期记忆两种实现方式：

- `ConversationBufferMemory` 是短期记忆——把最近的对话保存在内存里，每次调用 Agent 时带上，让它知道之前聊了什么
- `Chroma` 向量数据库是长期记忆——把信息转成数学向量存到磁盘，下次可以通过"语义相似度"检索出来

```python
from langchain.memory import ConversationBufferMemory
from langchain.vectorstores import Chroma
from langchain.embeddings import OpenAIEmbeddings

# 短期记忆
memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)

# 长期记忆（向量存储）
embeddings = OpenAIEmbeddings()
vectorstore = Chroma(
    embedding_function=embeddings,
    persist_directory="./agent_memory"
)

# 存储信息
vectorstore.add_texts(["用户喜欢日本料理", "用户偏好早上开会"])

# 检索相关信息
results = vectorstore.similarity_search("用户饮食偏好", k=3)
```

## 3️⃣ 工具使用能力（Tool Use）

工具使用使 Agent 能够扩展自身能力，访问外部资源和执行实际操作。

### 工具类型

| 工具类型 | 示例 | 用途 |
|----------|------|------|
| API 工具 | 天气 API、搜索 API | 获取实时信息 |
| 计算工具 | Python 解释器 | 执行代码和计算 |
| 数据库工具 | SQL、NoSQL | 数据存储和查询 |
| 文件工具 | 读写文件 | 文档处理 |
| 网络工具 | 爬虫、浏览器 | 网页交互 |

### 一个现实判断

工具使用的重点不是“能接多少工具”，而是：

- 工具描述是否清楚
- 参数是否明确
- 错误是否可处理
- 是否真的有必要交给工具完成

### 创建自定义工具

下面的代码演示了如何创建一个 BMI 计算工具。`@tool` 装饰器把普通 Python 函数变成 Agent 可以调用的工具。关键是：工具的文档字符串（docstring）会被 Agent 读取，用来判断什么时候该调用这个工具。

```python
from langchain.tools import tool
from typing import Optional

@tool
def calculate_bmi(weight: float, height: float) -> str:
    """
    计算身体质量指数 (BMI)
    
    参数:
        weight: 体重 (kg)
        height: 身高 (m)
    
    返回:
        BMI 值和健康建议
    """
    bmi = weight / (height ** 2)
    
    if bmi < 18.5:
        category = "偏瘦"
    elif bmi < 24:
        category = "正常"
    elif bmi < 28:
        category = "偏胖"
    else:
        category = "肥胖"
    
    return f"您的 BMI 为 {bmi:.1f}，属于{category}范围"

# 使用工具
result = calculate_bmi.invoke({"weight": 70, "height": 1.75})
print(result)  # 输出：您的 BMI 为 22.9，属于正常范围
```

## 4️⃣ 反思能力（Reflection）

反思使 Agent 能够从错误中学习，持续优化行为和输出质量。

### 反思机制

```
行动 → 观察结果 → 评估 → 调整策略 → 改进行动
  ↑                                        │
  └────────────────────────────────────────┘
```

### ReAct 框架

ReAct (Reasoning + Acting) 结合推理和行动：

```python
react_prompt = """
思考：我需要先查询北京的天气情况
行动：weather_api
行动输入：{"city": "北京"}
观察：北京今天晴朗，温度 25°C
思考：现在我可以建议用户适合户外活动
最终答案：北京今天天气很好，适合外出游玩！
"""
```

### 一个现实判断

反思的价值不在“让模型看起来更聪明”，而在于帮助系统减少重复犯错、提升长期稳定性。

### 自我评估示例

下面的代码展示了反思机制的两种用法：

1. `self_reflect` 函数：Agent 完成一次行动后，自动记录"做了什么、结果如何、学到了什么教训、下一步怎么改进"
2. `improved_action` 函数：在执行下一次任务时，参考之前的反思结果来调整策略

这就像一个员工在每次做完项目后写复盘总结，下次遇到类似任务时翻看笔记避免犯同样的错。

```python
def self_reflect(task, action, result):
    """
    自我反思函数
    """
    reflection = {
        "task": task,
        "action_taken": action,
        "result": result,
        "success": evaluate_result(result),
        "lessons_learned": extract_lessons(result),
        "improvement_plan": generate_improvement_plan(result)
    }
    return reflection

# 使用反思优化后续行动
def improved_action(task, previous_reflections):
    # 基于历史反思调整策略
    pass
```

## 🎯 能力整合

真正能用的 Agent，往往不是某个单点能力特别强，而是这些能力之间的衔接足够顺。

一个完整的 Agent 需要整合所有核心能力：

```python
class AdvancedAgent:
    def __init__(self):
        self.llm = load_llm()
        self.memory = MemorySystem()
        self.tools = ToolRegistry()
        self.planner = TaskPlanner()
    
    def execute(self, task):
        # 1. 检索相关记忆
        context = self.memory.retrieve(task)
        
        # 2. 规划任务
        plan = self.planner.decompose(task)
        
        # 3. 执行子任务
        for step in plan:
            # 选择合适工具
            tool = self.tools.select(step)
            # 执行并观察
            result = tool.execute(step)
            # 反思和优化
            reflection = self.reflect(step, result)
            # 更新记忆
            self.memory.store(step, result, reflection)
        
        # 4. 整合结果
        return self.synthesize(plan, context)
```

> **代码解读**（读不懂代码看这里）：这个类把四大核心能力串在了一起——先查记忆有没有相关信息，再把任务拆成步骤，每一步选择工具执行、反思结果、存入记忆，最后把所有结果汇总输出。这就是一个完整 Agent 的"骨架"。

## 📊 能力评估

| 能力 | 评估指标 | 优化方法 |
|------|----------|----------|
| 规划 | 任务完成率、步骤效率 | 更好的提示工程、ToT |
| 记忆 | 检索准确率、相关性 | 向量嵌入优化、索引策略 |
| 工具 | 调用成功率、响应时间 | 错误处理、缓存机制 |
| 反思 | 改进幅度、学习速度 | 反馈循环、评估模型 |

## 🔧 实践建议

1. **从简单开始**：先实现单一能力，再逐步整合
2. **重视记忆**：好的记忆系统大幅提升 Agent 表现
3. **工具封装**：为常用功能创建标准化工具接口
4. **持续反思**：建立自动化的反思和改进机制

---

**上一章**: [什么是 AI Agent](什么是 AI Agent.md) | **下一章**: [Agent 应用场景](Agent 应用场景.md)
