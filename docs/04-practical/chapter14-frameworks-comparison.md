# 框架对比

主流 AI Agent 框架的全面对比。

## 🤔 30 秒选择法

不知道选哪个？按顺序回答：

1. **你的项目需要多 Agent 协作吗？**
   - 是 → 跳到 #2
   - 否（单 Agent 就够） → **选 LangChain**

2. **团队需要快速上手吗？**
   - 是 → **选 CrewAI**（角色概念直觉化）
   - 否（愿意投入学习时间） → **选 AutoGen**（协作能力最强）

3. **还在纠结？**
   - 先用 CrewAI 做原型验证，成功后再评估是否迁移到 LangChain/AutoGen

## 📊 功能对比表

| 特性 | LangChain | AutoGen | CrewAI |
|------|-----------|---------|--------|
| **核心定位** | 通用 LLM 框架 | 多 Agent 协作 | 角色驱动团队 |
| **学习曲线** | 中等 | 中等 | 简单 |
| **多 Agent** | 支持 | ⭐⭐⭐ 核心特性 | ⭐⭐⭐ 核心特性 |
| **工具生态** | ⭐⭐⭐ 最丰富 | 中等 | 中等 |
| **记忆系统** | ⭐⭐⭐ 完善 | 基础 | 基础 |
| **代码执行** | 支持 | ⭐⭐⭐ 内置 | 支持 |
| **人类介入** | 支持 | ⭐⭐⭐ 灵活 | 支持 |
| **生产就绪** | ⭐⭐⭐ 成熟 | 较成熟 | 发展中 |
| **社区规模** | ⭐⭐⭐ 最大 | 大 | 中等 |
| **文档质量** | ⭐⭐⭐ 优秀 | 良好 | 良好 |

## 🎯 选择指南

### 选择 LangChain 如果：

- ✅ 需要丰富的工具集成
- ✅ 构建复杂的单 Agent 应用
- ✅ 需要完善的记忆和链式功能
- ✅ 重视社区支持和文档
- ✅ 计划长期维护的生产项目

**典型场景**：
- 客服系统
- 数据分析助手
- 文档处理自动化

### 选择 AutoGen 如果：

- ✅ 需要多 Agent 深度协作
- ✅ 需要代码执行能力
- ✅ 需要灵活的人类介入机制
- ✅ 研究多 Agent 交互模式
- ✅ 微软技术栈用户

**典型场景**：
- 软件开发团队模拟
- 复杂问题求解
- 研究和实验

### 选择 CrewAI 如果：

- ✅ 需要清晰的角色分工
- ✅ 快速搭建多 Agent 团队
- ✅ 任务流程相对固定
- ✅ 重视易用性和可读性
- ✅ 初创项目快速验证

**典型场景**：
- 内容创作团队
- 市场调研
- 报告生成

## 💡 组合使用

实际项目中可以组合使用：

```python
# LangChain + CrewAI
from langchain.tools import tool
from crewai import Agent, Task

# 使用 LangChain 工具
@tool
def search(query: str):
    """搜索工具"""
    pass

# 在 CrewAI 中使用
researcher = Agent(
    role='研究员',
    tools=[search]  # LangChain 工具
)
```

## 📈 发展趋势

| 框架 | 2026 状态 | 发展方向 |
|------|----------|----------|
| LangChain | 成熟稳定 | LangGraph（有状态工作流）、Deep Agents、LangSmith 可观测性 |
| AutoGen | v0.4 大重构 | AgentChat UI、跨语言支持、企业级部署 |
| CrewAI | 快速成长 | CrewAI Enterprise、知识库集成、Flow 编排 |
| OpenClaw | 社区活跃 | Skill 生态、多渠道集成、本地化 Agent |

## 🎓 学习建议

1. **入门**：从 CrewAI 开始，概念清晰
2. **进阶**：学习 LangChain，掌握完整生态
3. **专业**：研究 AutoGen，深入多 Agent 协作
4. **实践**：根据项目需求选择合适框架

## 🔧 迁移考虑

从 A 框架迁移到 B 框架时：

1. **抽象层**：将工具、记忆等抽象为接口
2. **适配层**：为不同框架编写适配器
3. **测试**：确保行为一致性
4. **渐进式**：逐步迁移，不要一次性重写

## 📚 参考资源

- [LangChain 文档](https://python.langchain.com/)
- [AutoGen 文档](https://microsoft.github.io/autogen/)
- [CrewAI 文档](https://docs.crewai.com/)

---

**上一章**: [CrewAI 入门](chapter14-3-crewai.md) | **下一章**: [Agent 应用案例](chapter15-use-cases.md)
