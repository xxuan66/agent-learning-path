# LangGraph 入门

> LangGraph 是 LangChain 生态中用于构建有状态、多步骤 Agent 应用的框架。

---

## 为什么需要 LangGraph

传统 Agent 的局限：
- **线性执行**：只能按顺序执行，难以处理复杂流程
- **状态管理困难**：难以维护复杂的状态
- **分支和循环**：实现复杂

LangGraph 的优势：
- **图结构**：用图定义 Agent 工作流
- **状态管理**：内置状态管理机制
- **灵活控制**：支持分支、循环、并行

---

## 📦 安装

```bash
pip install langgraph
```

---

## 🎯 核心概念

### StateGraph（状态图）

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict, Annotated
import operator

# 定义状态
class AgentState(TypedDict):
    messages: Annotated[list, operator.add]
    current_step: str

# 创建图
workflow = StateGraph(AgentState)
```

### Node（节点）

```python
def agent_node(state: AgentState) -> AgentState:
    """Agent 节点"""
    messages = state["messages"]
    response = llm.invoke(messages)
    return {"messages": [response]}

def tool_node(state: AgentState) -> AgentState:
    """工具调用节点"""
    last_message = state["messages"][-1]
    # 执行工具调用
    result = execute_tool(last_message)
    return {"messages": [result]}

# 添加节点
workflow.add_node("agent", agent_node)
workflow.add_node("tool", tool_node)
```

### Edge（边）

```python
# 条件边
def should_continue(state: AgentState) -> str:
    """决定下一步"""
    last_message = state["messages"][-1]
    if last_message.tool_calls:
        return "tool"
    return END

# 添加边
workflow.add_conditional_edges(
    "agent",
    should_continue,
    {
        "tool": "tool",
        END: END
    }
)

# 普通边
workflow.add_edge("tool", "agent")
```

### Entry Point（入口）

```python
# 设置入口
workflow.set_entry_point("agent")
```

---

## 🚀 完整示例：ReAct Agent

```python
from langgraph.graph import StateGraph, END
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langchain_core.messages import HumanMessage, AIMessage
from typing import TypedDict, Annotated, List
import operator

# 定义状态
class AgentState(TypedDict):
    messages: Annotated[List, operator.add]

# 定义工具
@tool
def search(query: str) -> str:
    """搜索信息"""
    return f"搜索结果：{query}"

@tool
def calculate(expression: str) -> str:
    """计算数学表达式"""
    import ast
    import operator as op
    
    operators = {
        ast.Add: op.add, ast.Sub: op.sub,
        ast.Mult: op.mul, ast.Div: op.truediv,
    }
    
    def eval_expr(node):
        if isinstance(node, ast.Expression):
            return eval_expr(node.body)
        elif isinstance(node, ast.Constant):
            return node.value
        elif isinstance(node, ast.BinOp):
            return operators[type(node.op)](eval_expr(node.left), eval_expr(node.right))
        raise ValueError("不支持的表达式")
    
    try:
        tree = ast.parse(expression, mode='eval')
        return str(eval_expr(tree))
    except Exception as e:
        return f"错误：{e}"

# 初始化 LLM
llm = ChatOpenAI(model="gpt-4", temperature=0)
tools = [search, calculate]
llm_with_tools = llm.bind_tools(tools)

# Agent 节点
def agent_node(state: AgentState) -> AgentState:
    messages = state["messages"]
    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

# 工具节点
def tool_node(state: AgentState) -> AgentState:
    last_message = state["messages"][-1]
    tool_calls = last_message.tool_calls
    
    results = []
    for tool_call in tool_calls:
        tool_name = tool_call["name"]
        tool_args = tool_call["args"]
        
        # 找到对应工具并执行
        for tool in tools:
            if tool.name == tool_name:
                result = tool.invoke(tool_args)
                results.append({
                    "tool_call_id": tool_call["id"],
                    "content": result
                })
                break
    
    return {"messages": results}

# 条件判断
def should_continue(state: AgentState) -> str:
    last_message = state["messages"][-1]
    if hasattr(last_message, 'tool_calls') and last_message.tool_calls:
        return "tool"
    return END

# 构建图
workflow = StateGraph(AgentState)
workflow.add_node("agent", agent_node)
workflow.add_node("tool", tool_node)
workflow.set_entry_point("agent")
workflow.add_conditional_edges("agent", should_continue)
workflow.add_edge("tool", "agent")

# 编译图
app = workflow.compile()

# 使用
result = app.invoke({
    "messages": [HumanMessage(content="帮我计算 123 * 456")]
})
print(result["messages"][-1].content)
```

---

## 🔄 循环和分支

### 循环示例

```python
# 最大循环次数
max_iterations = 5
iteration = 0

def should_continue(state: AgentState) -> str:
    global iteration
    iteration += 1
    
    if iteration >= max_iterations:
        return END
    
    last_message = state["messages"][-1]
    if hasattr(last_message, 'tool_calls') and last_message.tool_calls:
        return "tool"
    return END
```

### 分支示例

```python
def router(state: AgentState) -> str:
    """根据状态路由到不同节点"""
    last_message = state["messages"][-1].content
    
    if "搜索" in last_message:
        return "search_node"
    elif "计算" in last_message:
        return "calculate_node"
    else:
        return "general_node"

workflow.add_conditional_edges(
    "input",
    router,
    {
        "search_node": "search_node",
        "calculate_node": "calculate_node",
        "general_node": "general_node"
    }
)
```

---

## 📊 状态管理

### 持久化状态

```python
from langgraph.checkpoint.sqlite import SqliteSaver

# 使用 SQLite 存储状态
memory = SqliteSaver.from_conn_string(":memory:")

# 编译时添加 checkpointer
app = workflow.compile(checkpointer=memory)

# 使用 thread_id 追踪对话
config = {"configurable": {"thread_id": "1"}}
result = app.invoke({"messages": [HumanMessage(content="你好")]}, config)
```

### 状态恢复

```python
# 获取之前的状态
config = {"configurable": {"thread_id": "1"}}
state = app.get_state(config)

# 从之前的状态继续
result = app.invoke(
    {"messages": [HumanMessage(content="继续")]},
    config
)
```

---

## 🤝 Multi-Agent 示例

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict, Annotated, List

class MultiAgentState(TypedDict):
    messages: Annotated[List, operator.add]
    current_agent: str
    task: str

# 研究员 Agent
def researcher_node(state: MultiAgentState) -> MultiAgentState:
    task = state["task"]
    # 执行研究
    research_result = f"研究结果：{task}"
    return {
        "messages": [AIMessage(content=research_result)],
        "current_agent": "writer"
    }

# 写手 Agent
def writer_node(state: MultiAgentState) -> MultiAgentState:
    messages = state["messages"]
    # 基于研究结果写作
    article = f"文章：基于 {messages[-1].content} 的分析"
    return {
        "messages": [AIMessage(content=article)],
        "current_agent": "reviewer"
    }

# 审查员 Agent
def reviewer_node(state: MultiAgentState) -> MultiAgentState:
    messages = state["messages"]
    # 审查文章
    review = f"审查通过：{messages[-1].content}"
    return {"messages": [AIMessage(content=review)]}

# 路由
def route_by_agent(state: MultiAgentState) -> str:
    current = state.get("current_agent", "researcher")
    if current == "writer":
        return "writer"
    elif current == "reviewer":
        return "reviewer"
    return END

# 构建图
workflow = StateGraph(MultiAgentState)
workflow.add_node("researcher", researcher_node)
workflow.add_node("writer", writer_node)
workflow.add_node("reviewer", reviewer_node)
workflow.set_entry_point("researcher")
workflow.add_conditional_edges("researcher", route_by_agent)
workflow.add_conditional_edges("writer", route_by_agent)
workflow.add_edge("reviewer", END)

app = workflow.compile()
```

---

## 📈 可视化

```python
# 生成图的可视化
from langgraph.graph import StateGraph

# 获取图结构
graph = workflow.get_graph()

# 生成 Mermaid 图
print(graph.draw_mermaid())

# 或者生成 PNG
# graph.draw_png("workflow.png")
```

---

## 与其他框架对比

| 特性 | LangGraph | AutoGen | CrewAI |
|------|-----------|---------|--------|
| 状态管理 | ✅ 内置 | ⚠️ 手动 | ⚠️ 手动 |
| 图结构 | ✅ 原生 | ❌ | ❌ |
| 持久化 | ✅ 内置 | ❌ | ❌ |
| 可视化 | ✅ 支持 | ❌ | ❌ |
| 学习曲线 | 中等 | 简单 | 简单 |

---

## 📚 学习资源

- [官方文档](https://langchain-ai.github.io/langgraph/)
- [GitHub](https://github.com/langchain-ai/langgraph)
- [教程](https://langchain-ai.github.io/langgraph/tutorials/)

---

## 一个实用判断

如果你的需求是简单的单 Agent 任务，直接用 LangChain 就够了。但如果你需要：
- 多步骤、有状态的工作流
- 多 Agent 协作
- 复杂的分支和循环逻辑
- 状态持久化和恢复

那么 LangGraph 是更好的选择。

---

**作者：** xuan