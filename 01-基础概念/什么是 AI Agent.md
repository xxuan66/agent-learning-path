# 什么是 AI Agent？

## 📌 定义

**AI Agent（人工智能智能体）** 是一种能够感知环境、做出决策并执行行动以实现目标的智能系统。

简单来说，AI Agent = 大语言模型（LLM）+ 规划能力 + 记忆能力 + 工具使用能力

## 🧠 核心组成

### 1. 大脑：大语言模型（LLM）

LLM 是 Agent 的核心控制器，负责：
- 理解用户输入和环境信息
- 进行推理和决策
- 生成响应和行动指令

```python
# 简单的 Agent 示例
from langchain.agents import create_agent

def get_weather(city: str) -> str:
    """获取天气信息"""
    return f"{city} 今天晴朗，温度 25°C"

# 创建一个简单的 Agent
agent = create_agent(
    model="gpt-4",
    tools=[get_weather],
    system_prompt="你是一个有帮助的助手"
)

# 使用 Agent
response = agent.invoke({"messages": [{"role": "user", "content": "北京天气怎么样？"}]})
print(response)
```

### 2. 规划模块

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

- **短期记忆**：当前对话上下文
- **长期记忆**：向量数据库存储的历史信息

### 4. 工具模块

外部能力扩展：
- API 调用（天气、搜索、数据库等）
- 代码执行
- 文件操作
- 网络爬虫

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
