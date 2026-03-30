# Agent 学习路径 (Agent Learning Path)

系统化的中文 Agent 学习仓库，面向希望从概念、技术演进、核心组件，一路走到框架、案例和最佳实践的读者。

![Status](https://img.shields.io/badge/Status-✅_已完成-green)
![Language](https://img.shields.io/badge/Language-中文-green)
![Level](https://img.shields.io/badge/Level-入门到精通-orange)
![Updated](https://img.shields.io/badge/updated-2026--03--30-blue)

---

## 这份仓库适合谁

- 想系统理解 Agent，而不是只看零散热点概念的读者
- 想分清 ReAct、Planning、Tool Use、Memory、Multi-Agent 等概念边界的读者
- 想从“会用模型”走向“会判断 Agent 系统怎么设计”的开发者

## 建议从哪套目录开始读

当前仓库以顶层中文目录为主入口：

- `01-基础概念/`
- `02-技术演进/`
- `03-核心组件/`
- `04-主流框架/`
- `05-实战案例/`
- `06-学习资源/`
- `07-最佳实践/`

`docs/` 目录保留了更早期的分层稿，当前更适合做历史参考，不建议作为主要阅读入口。

## 📚 目录

### 第一部分：基础概念
- [什么是 AI Agent](01-基础概念/什么是%20AI%20Agent.md)
- [Agent 核心能力](01-基础概念/Agent%20核心能力.md)
- [Agent 应用场景](01-基础概念/Agent%20应用场景.md)

### 第二部分：技术演进
- [Agent 发展简史](02-技术演进/Agent%20发展简史.md)
- [里程碑项目](02-技术演进/里程碑项目.md)

### 第三部分：核心组件
- [规划与任务分解](03-核心组件/规划与任务分解.md)
- [记忆系统](03-核心组件/记忆系统.md)

### 第四部分：主流框架
- [框架对比](04-主流框架/框架对比.md)
- [LangChain 入门](04-主流框架/LangChain%20入门.md)
- [AutoGen 入门](04-主流框架/AutoGen%20入门.md)
- [CrewAI 入门](04-主流框架/CrewAI%20入门.md)

### 第五部分：实战案例
- [客服助手](05-实战案例/客服助手.md)

### 第六部分：学习资源
- [在线课程](06-学习资源/在线课程.md)
- [技术博客](06-学习资源/技术博客.md)
- [开源项目](06-学习资源/开源项目.md)

### 第七部分：最佳实践
- [开发规范](07-最佳实践/开发规范.md)
- [安全指南](07-最佳实践/安全指南.md)
- [性能优化](07-最佳实践/性能优化.md)

---

## 🎯 学习目标

1. **理解 Agent 的本质** - 从基本概念到高级架构
2. **掌握核心技术** - ReAct、CoT、Planning、Tool Use 等
3. **动手实践** - 构建自己的 Agent 应用
4. **了解前沿动态** - 理解 Agent 的主要发展方向和系统变化

---

## 📖 推荐阅读顺序

```text
基础概念 → 技术演进 → 核心组件 → 主流框架 → 实战案例 → 最佳实践
```

---

## 🔗 相关资源

- [OpenAI Agents Documentation](https://platform.openai.com/docs/assistants)
- [Anthropic Claude Tool Use](https://docs.anthropic.com/en/docs/tool-use)
- [LangChain Agents](https://python.langchain.com/docs/modules/agents/)
- [AutoGen Papers](https://github.com/microsoft/autogen)

---

## 📊 当前状态

| 模块 | 状态 | 说明 |
|------|------|------|
| 基础概念 | ✅ 已整理 | 作为当前主入口的一部分 |
| 技术演进 | ✅ 已整理 | 用于建立历史脉络 |
| 核心组件 | ✅ 已整理 | 聚焦 Agent 关键模块 |
| 主流框架 | ✅ 已整理 | 聚焦框架与选型 |
| 实战案例 | ✅ 已整理 | 当前已有主案例，后续可继续扩展 |
| 学习资源 | ✅ 已整理 | 作为补充资料使用 |
| 最佳实践 | ✅ 已整理 | 偏工程与安全视角 |

**说明：** 当前仓库已经覆盖 Agent 学习主线，但部分章节仍可继续精修与补充。

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

详见：[CONTRIBUTING.md](CONTRIBUTING.md)

---

**最后更新：** 2026-03-30  
**维护者：** xuan  
**GitHub：** https://github.com/xxuan66/agent-learning-path

## 🆕 近期更新

- ✅ 将仓库主入口统一到顶层中文目录
- ✅ 系统重写基础概念、技术演进、核心组件、框架与案例导读
- ✅ 补强资源导航、最佳实践、安全与性能相关说明
- ✅ 更新客服助手案例的降级和稳定性设计
- ✅ 清理仓库整理说明与 `.gitignore`

## 🗺️ Agent 学习路线图

### 阶段一：基础入门（1-2 周）

- [ ] 理解 LLM 基本原理
- [ ] 学习 Prompt Engineering 基础
- [ ] 熟悉 OpenClaw/类似 Agent 框架

### 阶段二：工具使用（2-3 周）

- [ ] 掌握 Tool Use / Function Calling
- [ ] 实践 RAG（检索增强生成）
- [ ] 学习向量数据库基础

### 阶段三：Agent 开发（3-4 周）

- [ ] 理解 ReAct 模式
- [ ] 实现简单 Agent
- [ ] 学习多 Agent 协作

### 阶段四：高级应用（持续）

- [ ] 构建复杂工作流
- [ ] 优化 Agent 性能
- [ ] 参与开源贡献

---
