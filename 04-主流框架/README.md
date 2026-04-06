# 04-主流框架

这一部分不是框架宣传页，而是帮助你建立一个更现实的问题：不同 Agent 框架分别擅长什么，适合什么团队，以及应该如何选择。

## 本章内容

- [LangChain 入门](LangChain%20入门.md) - 最流行的 Agent 框架
- [LangGraph 入门](LangGraph%20入门.md) 🆕 - LangChain 的状态图框架
- [AutoGen 入门](AutoGen%20入门.md) - 微软多 Agent 协作框架
- [CrewAI 入门](CrewAI%20入门.md) - 角色驱动的 Agent 团队
- [框架对比](框架对比.md) - 如何选择适合的框架

## 框架生态概览

```
┌─────────────────────────────────────────────────┐
│            AI Agent 框架生态                     │
├─────────────────────────────────────────────────┤
│                                                  │
│  🔷 通用框架                                     │
│     LangChain · LlamaIndex · Haystack           │
│                                                  │
│  🔶 多 Agent 协作                                │
│     AutoGen · CrewAI · ChatDev                  │
│                                                  │
│  🔷 专业框架                                     │
│     Semantic Kernel (微软)                       │
│     Vertex AI Agent Builder (Google)            │
│                                                  │
│  🔶 基础设施                                     │
│     Chroma · Pinecone · Weaviate (向量库)        │
│     Redis · Memcached (缓存)                     │
│                                                  │
└─────────────────────────────────────────────────┘
```

## 学习目标

1. 理解主流框架各自的定位
2. 建立更实际的框架选择标准
3. 理解框架能力和项目复杂度之间的关系
4. 为后续实战案例选型打基础

## 一个简单建议

如果你第一次接触 Agent 框架，建议先看：

1. `框架对比.md`
2. 再按自己的目标跳转到 LangChain、AutoGen 或 CrewAI

---

**上一章**: [03-核心组件](../03-核心组件/README.md) | **下一章**: [05-实战案例](../05-实战案例/README.md)
