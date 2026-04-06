<div align="center">

# 🚀 Agent 学习路径

**从 ReAct 到 Multi-Agent，从概念到实战**

*这是你成为 Agent 架构师的系统化路线图*

![Stars](https://img.shields.io/github/stars/xxuan66/agent-learning-path?style=social)
![Status](https://img.shields.io/badge/Status-持续更新-green)
![Language](https://img.shields.io/badge/Language-中文-green)
![Articles](https://img.shields.io/badge/文章-35篇-blue)

[快速开始](#-内容导航) • [学习路线](#-学习路线图) • [设计模式](#-为什么这些内容值得读)

</div>

---

## 💡 为什么是这个仓库？

你可能看过很多 Agent 相关的文章，但：

```
❌ 看了很多零散的概念，依然不知道怎么设计 Agent
❌ 会调用 API，但不理解 Agent 背后的设计原理
❌ 踩了很多坑，却不知道这些坑早就有人踩过
❌ 追了很多热点，却缺少系统化的知识体系
```

**这个仓库要解决的，就是这些问题。**

---

## ✨ 你能得到什么

| 内容 | 数量 | 价值 |
|------|------|------|
| 📚 系统化知识体系 | 10 大模块 | 从概念到架构 |
| 🛠️ 可运行的实战案例 | 4 个完整案例 | 复制即用 |
| 🎯 Agent 设计模式 | 4 种核心模式 | 像设计模式 23 种 |
| 💥 真实踩坑记录 | 5 个失败案例 | 避免重复踩坑 |
| 🔍 产品架构拆解 | 4 款产品深度分析 | 学习最佳实践 |
| 📰 月度动态更新 | 持续更新 | 跟踪最新趋势 |

---

## 📖 内容导航

### 🟢 入门篇（1-2 周）

> 从零建立 Agent 的完整认知

- [什么是 AI Agent](01-基础概念/什么是%20AI%20Agent.md) - 核心概念
- [Agent 核心能力](01-基础概念/Agent%20核心能力.md) - 能力边界
- [Agent 发展简史](02-技术演进/Agent%20发展简史.md) - 技术脉络

### 🟡 进阶篇（2-3 周）

> 掌握 Agent 的核心模块

- [规划与任务分解](03-核心组件/规划与任务分解.md) - Agent 的"思考"
- [记忆系统](03-核心组件/记忆系统.md) - Agent 的"记忆"
- [Multi-Agent 协作](03-核心组件/Multi-Agent%20协作.md) 🆕 - 多 Agent 协同
- [评估与测试](03-核心组件/评估与测试.md) 🆕 - 如何衡量 Agent
- [调试与可观测性](03-核心组件/调试与可观测性.md) 🆕 - 如何调试 Agent

### 🔴 实战篇（3-4 周）

> 真实场景，完整代码

- [客服助手](05-实战案例/客服助手.md) - 入门案例
- [数据分析 Agent](05-实战案例/数据分析Agent.md) 🆕 - 数据处理全流程
- [代码生成 Agent](05-实战案例/代码生成Agent.md) 🆕 - 代码自动生成
- [研究助手 Agent](05-实战案例/研究助手Agent.md) 🆕 - 信息搜集与报告

### ⚫ 高级篇

> 设计模式、产品拆解、踩坑经验

- [Agent 设计模式](08-设计模式/README.md) 🆕 - ReAct、规划、反思、投票
- [产品架构拆解](09-产品拆解/README.md) 🆕 - Cursor、Perplexity、GPTs、Devin
- [失败案例与踩坑记录](07-最佳实践/失败案例与踩坑记录.md) 🆕 - 真实教训

---

## 🗺️ 学习路线图

```
                    ┌─────────────────┐
                    │   你在这里 👋    │
                    └────────┬────────┘
                             │
         ┌───────────────────┼───────────────────┐
         ▼                   ▼                   ▼
    ┌─────────┐        ┌─────────┐        ┌─────────┐
    │  入门   │        │  进阶   │        │  实战   │
    │ 1-2 周  │   ►    │ 2-3 周  │   ►    │ 3-4 周  │
    └─────────┘        └─────────┘        └─────────┘
         │                   │                   │
         ▼                   ▼                   ▼
    基础概念            核心组件            实战案例
    技术演进            框架对比            设计模式
                        Multi-Agent         产品拆解
```

---

## 🔥 为什么这些内容值得读？

### 设计模式：Agent 界的"23 种设计模式"

```
ReAct 模式  → 边想边做，最基础也最常用
规划模式    → 先规划再执行，处理复杂任务
反思模式    → 自我评估，持续改进
投票模式    → 多 Agent 投票，集体决策
```

### 踩坑记录：前人踩过的坑，你不必再踩

```
💥 Agent 陷入无限循环
💥 Token 消耗失控
💥 工具返回格式不一致
💥 Agent 幻觉调用不存在的工具
💥 并发请求导致状态混乱
```

### 产品拆解：学习顶级产品的设计思路

```
🔍 Cursor   - AI 代码编辑器的架构设计
🔍 Perplexity - AI 搜索引擎的工作流程
🔍 GPTs     - OpenAI 的 Agent 配置化方案
🔍 Devin    - AI 软件工程师的端到端设计
```

---

## 📊 仓库数据

| 指标 | 数值 |
|------|------|
| 内容模块 | 10 个 |
| 文章总数 | 35+ 篇 |
| 实战案例 | 4 个 |
| 设计模式 | 4 种 |
| 产品拆解 | 4 款 |
| 踩坑记录 | 5 个 |

---

## 🤝 谁在维护这个仓库

这是一个持续更新的仓库。

- 📅 每月更新「Agent 月报」，跟踪最新动态
- 🔄 持续补充新的实战案例
- 🐛 欢迎提交 Issue 和 PR

**如果你觉得有用，欢迎 ⭐ Star 支持！**

---

<div align="center">

## 📚 相关资源

[OpenAI Agents](https://platform.openai.com/docs/assistants) • [Anthropic Tool Use](https://docs.anthropic.com/en/docs/tool-use) • [LangChain](https://python.langchain.com/docs/modules/agents/) • [AutoGen](https://github.com/microsoft/autogen)

---

**从 ReAct 到 Multi-Agent，从概念到架构**

*系统化的 Agent 学习路径，助你成为 Agent 架构师*

Made with ❤️ by [xuan](https://github.com/xxuan66)

</div>