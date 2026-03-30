# CrewAI 入门

CrewAI 的核心特点是“角色驱动”和“任务编排表达清晰”。它很适合用来构建多角色协作流程，也很适合教学和快速原型验证。

## 适合什么人

- 想快速搭建多 Agent 原型的人
- 更喜欢“角色 + 任务 + 团队”这种表达方式的人
- 想把协作流程写得更清晰可读的人

## 📦 安装

```bash
pip install crewai
```

## 🎯 核心概念

### Agent（智能体）

```python
from crewai import Agent

# 创建 Agent
researcher = Agent(
    role='高级研究员',
    goal='深入调研主题并提供准确信息',
    backstory='你是一位经验丰富的研究员，擅长信息收集和分析',
    verbose=True,
    allow_delegation=False
)
```

### Task（任务）

```python
from crewai import Task

# 创建任务
research_task = Task(
    description='调研 AI Agent 的最新发展趋势',
    expected_output='一份包含 5 个关键趋势的报告',
    agent=researcher
)
```

### Crew（团队）

```python
from crewai import Crew

# 创建团队
crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, write_task],
    verbose=True
)

# 执行
result = crew.kickoff()
```

## 🚀 完整示例

```python
from crewai import Agent, Task, Crew, Process

# 定义角色
researcher = Agent(
    role='首席研究员',
    goal='发现 AI 领域的最新突破',
    backstory='你在 AI 研究领域有 10 年经验',
    verbose=True
)

writer = Agent(
    role='科技作家',
    goal='将复杂概念转化为易懂的文章',
    backstory='你是知名科技博主，擅长科普写作',
    verbose=True
)

# 定义任务
research_task = Task(
    description='调研 2024 年 AI 领域的重大突破',
    expected_output='包含 5 个突破点的列表',
    agent=researcher
)

write_task = Task(
    description='基于调研结果写一篇科普文章',
    expected_output='一篇 1500 字的文章',
    agent=writer
)

# 创建团队
crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, write_task],
    process=Process.sequential,  # 顺序执行
    verbose=True
)

# 执行
result = crew.kickoff()
print(result)
```

## 一个实用判断

如果你更关注“团队协作感”和“任务流程表达”，CrewAI 很容易上手；如果你需要更底层、更细粒度的控制，可能还要结合其他框架或自定义实现。

## 📊 执行流程

```
Process.sequential（顺序）:
任务 1 → 任务 2 → 任务 3

Process.hierarchical（层级）:
     经理 Agent
    /    |    \
任务 1  任务 2  任务 3
```

## 🛠️ 高级功能

### 工具集成

```python
from crewai_tools import SerperDevTool

# 添加搜索工具
researcher.tools = [SerperDevTool()]
```

### 记忆功能

```python
crew = Crew(
    agents=[...],
    tasks=[...],
    memory=True  # 启用记忆
)
```

## 📚 学习资源

- [官方文档](https://docs.crewai.com/)
- [GitHub](https://github.com/joaomdmoura/crewai)

---

**上一章**: [AutoGen 入门](AutoGen 入门.md) | **下一章**: [框架对比](框架对比.md)
