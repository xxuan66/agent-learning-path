# 什么是 AI Agent？

## 先给一个更稳妥的定义

**AI Agent（人工智能智能体）** 可以理解为一种围绕目标运行的系统：它不仅会生成文本，还会根据环境信息做判断、调用工具、执行动作，并根据结果继续调整下一步。

如果只用一句更实用的话来概括：

> Agent = 模型能力 + 任务目标 + 外部工具 + 状态管理 + 执行循环

这比简单地把 Agent 理解成“更聪明的聊天机器人”更接近真实情况。

## 为什么它不只是聊天助手

普通聊天助手通常完成的是“输入一句，输出一句”的任务；而 Agent 更强调：

- 有明确目标
- 可以分步执行
- 可以调用外部能力
- 可以根据执行结果继续调整

所以 Agent 的关键不只是语言生成，而是围绕目标形成闭环。

## 🧠 核心组成

### 1. 大脑：大语言模型（LLM）

LLM 是 Agent 的核心控制器，负责：
- 理解用户输入和环境信息
- 进行推理和决策
- 生成响应和行动指令

下面这段代码展示了一个最简 Agent 的完整流程。读不懂代码没关系，只需要理解它做了什么：

1. 定义了一个"查天气"的工具（`get_weather`）
2. 创建了一个大语言模型实例（GPT-4）
3. 把模型和工具组装成一个 Agent
4. 用户用自然语言提问，Agent 自动决定调用"查天气"工具并返回结果

```python
# 简单的 Agent 示例
from langchain_openai import ChatOpenAI
from langchain.agents import AgentExecutor, create_tool_calling_agent
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.tools import tool

@tool
def get_weather(city: str) -> str:
    """获取天气信息"""
    return f"{city} 今天晴朗，温度 25°C"

# 创建 LLM
llm = ChatOpenAI(model="gpt-4", temperature=0)

# 创建提示词模板
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个有帮助的助手"),
    ("human", "{input}"),
])

# 创建 Agent
tools = [get_weather]
agent = create_tool_calling_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

# 使用 Agent
response = agent_executor.invoke({"input": "北京天气怎么样？"})
print(response["output"])
```

> **关键理解**：用户只说了"北京天气怎么样"，Agent 自己判断出需要调用 `get_weather` 工具，传入参数 `city="北京"`，拿到结果后组织成自然语言回答。这就是 Agent 和普通聊天机器人的区别——它不只是生成文字，还能主动调用工具完成任务。

### 2. 规划模块

规划模块让 Agent 具备"拆解任务"的能力。当用户给出一个复杂目标时，Agent 不是直接尝试一步到位，而是先把大任务拆成几个小任务，再逐个执行。就像项目经理接到需求后，先列出任务清单，再分配给团队执行。

将复杂任务分解为可执行的子任务：

```
任务："帮我规划一次北京三日游"
    ↓
子任务 1: 查询北京天气
子任务 2: 查找北京景点
子任务 3: 预订酒店
子任务 4: 制定行程安排
```

### 3. 记忆模块

记忆模块解决的是"Agent 怎么记住东西"的问题。大模型本身没有记忆——每次调用都是全新的，不会记得上一轮对话说了什么。记忆模块就是为了解决这个问题而存在的。

- **短期记忆**：当前对话上下文——就像你和朋友聊天时，记得刚才说过的话
- **长期记忆**：向量数据库存储的历史信息——就像你的个人笔记，跨会话保留

### 4. 工具模块

工具模块让 Agent 能突破大模型本身的局限。大模型只会"说话"，不会"做事"——它不能查天气、不能搜网页、不能读文件。工具模块就是给 Agent 装上"手"，让它能和外部世界交互。

常见的工具类型：
- API 调用（查天气、搜索引擎、查数据库等）
- 代码执行（运行 Python 脚本进行计算）
- 文件操作（读写文档、整理数据）
- 网络爬虫（抓取网页内容）

## 🔄 Agent 工作流程

```
用户输入 → 理解意图 → 任务规划 → 工具调用 → 执行行动 → 结果反馈 → 输出响应
     ↑                                                                  │
     └─────────────────────── 记忆存储 ←────────────────────────────────┘
```

## 🆚 Agent vs 传统程序

| 特性 | 传统程序 | AI Agent |
|------|----------|----------|
| 决策方式 | 预设规则 | 自主推理 |
| 灵活性 | 低（固定流程） | 高（动态调整） |
| 学习能力 | 无 | 持续学习 |
| 适用范围 | 特定任务 | 通用任务 |
| 人机交互 | 命令式 | 自然语言 |

## 🎯 Agent 的特征

1. **自主性（Autonomy）**：能够独立做出决策和执行行动
2. **反应性（Reactivity）**：能够感知环境并做出响应
3. **主动性（Pro-activeness）**：能够主动追求目标
4. **社交性（Social Ability）**：能够与其他 Agent 或人类交互

## 📊 Agent 分类

### 按能力分类

- **简单反射 Agent**：基于当前感知直接行动
- **基于模型的 Agent**：维护内部状态模型
- **基于目标的 Agent**：为实现目标而规划
- **基于效用的 Agent**：优化决策效用
- **学习 Agent**：从经验中学习和改进

### 按应用分类

- **个人助手**：日程管理、邮件处理
- **客服 Agent**：自动问答、问题解决
- **研究 Agent**：文献检索、数据分析
- **编程 Agent**：代码生成、调试
- **游戏 Agent**：NPC 行为、策略决策

## 🔮 发展趋势

1. **多模态能力**：处理文本、图像、音频、视频
2. **多 Agent 协作**：多个 Agent 协同完成复杂任务
3. **具身智能**：与物理世界交互（机器人）
4. **自我进化**：持续学习和能力增强

## 📚 延伸阅读

- [LLM Powered Autonomous Agents - Lilian Weng](https://lilianweng.github.io/posts/2023-06-23-agent/)
- [LangChain 官方文档](https://python.langchain.com/)
- [AutoGen 论文](https://microsoft.github.io/autogen/)

---

**上一章**: [01-基础概念](README.md) | **下一章**: [Agent 核心能力](Agent 核心能力.md)
