# LangChain 入门

LangChain 更适合作为“通用 LLM / Agent 应用开发框架”来理解，而不只是一个单纯的 Agent 框架。它的优势在于生态广、组件多、资料多，但这也意味着抽象层较多，初学者有时会觉得概念偏杂。

## 适合什么人

- 想做单 Agent 或工具型 Agent 的开发者
- 想快速接模型、提示词、工具、向量库、记忆模块的人
- 想依赖成熟生态和大量示例的人

## 📦 安装

```bash
# 基础安装
pip install langchain

# 安装常用集成
pip install langchain-openai langchain-chroma langchain-community
```

## 🎯 核心概念

### 1. Model（模型）

```python
from langchain_openai import ChatOpenAI

# 初始化模型
llm = ChatOpenAI(
    model="gpt-4",
    temperature=0.7,
    max_tokens=1000
)

# 使用模型
response = llm.invoke("你好，请介绍一下自己")
print(response.content)
```

### 2. Prompt（提示）

```python
from langchain.prompts import ChatPromptTemplate

# 创建提示模板
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个{role}，擅长{expertise}"),
    ("user", "{input}")
])

# 使用模板
formatted = prompt.format_messages(
    role="编程助手",
    expertise="Python 开发",
    input="如何读取文件？"
)
```

### 3. Chain（链）

```python
from langchain.chains import LLMChain

# 创建链
chain = prompt | llm

# 执行链
result = chain.invoke({
    "role": "翻译助手",
    "expertise": "中英翻译",
    "input": "Hello, world!"
})
```

### 4. Agent（智能体）

```python
from langchain.agents import create_agent, Tool
from langchain.tools import tool

@tool
def search(query: str) -> str:
    """搜索互联网信息"""
    # 实现搜索逻辑
    return f"搜索结果：{query}"

@tool
def calculate(expression: str) -> str:
    """计算数学表达式"""
    return str(eval(expression))

# 创建 Agent
agent = create_agent(
    llm=llm,
    tools=[search, calculate],
    system_message="你是一个有帮助的助手"
)

# 使用 Agent
response = agent.invoke({
    "messages": [{"role": "user", "content": "搜索 AI 发展趋势，然后计算 123 * 456"}]
})
```

### 5. Memory（记忆）

```python
from langchain.memory import ConversationBufferMemory

# 创建记忆
memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)

# 在 Agent 中使用
agent = create_agent(
    llm=llm,
    tools=[],
    memory=memory
)
```

## 一个实用判断

如果你的重点是“把模型接到真实系统里”，LangChain 往往是不错的起点；如果你的重点是“把多个角色和任务流程编得很清楚”，则不一定是最直接的选择。

## 🛠️ 常用工具

### 内置工具

```python
from langchain_community.tools import DuckDuckGoSearchRun

# 搜索工具
search = DuckDuckGoSearchRun()
result = search.invoke("AI Agent 最新进展")
```

### 自定义工具

```python
from langchain.tools import BaseTool
from typing import Type
from pydantic import BaseModel, Field

class WeatherInput(BaseModel):
    city: str = Field(description="城市名称")

class WeatherTool(BaseTool):
    name = "weather"
    description = "查询城市天气"
    args_schema: Type[BaseModel] = WeatherInput
    
    def _run(self, city: str) -> str:
        # 调用天气 API
        return f"{city} 今天晴朗，25°C"
    
    async def _arun(self, city: str) -> str:
        # 异步实现
        return self._run(city)

# 使用工具
weather = WeatherTool()
result = weather.invoke({"city": "北京"})
```

## 📊 Agent 类型

### Zero-shot Agent

```python
from langchain.agents import create_openai_functions_agent

agent = create_openai_functions_agent(
    llm=llm,
    tools=tools,
    prompt=prompt
)
```

### ReAct Agent

```python
from langchain.agents import create_react_agent

agent = create_react_agent(
    llm=llm,
    tools=tools,
    prompt=prompt
)
```

### Structured Chat Agent

```python
from langchain.agents import create_structured_chat_agent

agent = create_structured_chat_agent(
    llm=llm,
    tools=tools,
    prompt=prompt
)
```

## 🔧 实战：构建客服 Agent

```python
from langchain.agents import create_agent
from langchain.tools import tool
from langchain.vectorstores import Chroma
from langchain.memory import ConversationBufferMemory

# 知识库
@tool
def search_knowledge_base(query: str) -> str:
    """从知识库搜索产品信息"""
    kb = Chroma(embedding_function=embeddings, persist_directory="./kb")
    results = kb.similarity_search(query, k=3)
    return "\n".join([r.page_content for r in results])

# 订单查询
@tool
def check_order(order_id: str) -> str:
    """查询订单状态"""
    # 调用订单 API
    return f"订单 {order_id} 已发货"

# 创建 Agent
memory = ConversationBufferMemory(return_messages=True)

agent = create_agent(
    llm=llm,
    tools=[search_knowledge_base, check_order],
    memory=memory,
    system_message="""你是一个客服助手。
- 先查询知识库回答产品问题
- 订单问题使用订单查询工具
- 保持友好专业的语气
"""
)

# 使用
response = agent.invoke({
    "messages": [{"role": "user", "content": "我的订单 12345 到哪了？"}]
})
```

## 📈 性能优化

### 1. 缓存

```python
from langchain.cache import InMemoryCache
from langchain.globals import set_llm_cache

# 启用缓存
set_llm_cache(InMemoryCache())

# 相同请求会返回缓存结果
```

### 2. 流式输出

```python
for chunk in llm.stream("写一首诗"):
    print(chunk.content, end="", flush=True)
```

### 3. 批量处理

```python
results = llm.batch([
    "问题 1",
    "问题 2",
    "问题 3"
])
```

## ⚠️ 注意事项

1. **API 成本**：注意 token 使用量
2. **错误处理**：添加重试和降级机制
3. **安全**：验证工具输入，防止注入攻击
4. **监控**：记录 Agent 行为便于调试

## 📚 学习资源

- [官方文档](https://python.langchain.com/)
- [GitHub](https://github.com/langchain-ai/langchain)
- [Discord 社区](https://discord.gg/langchain)

---

**上一章**: [04-主流框架](README.md) | **下一章**: [AutoGen 入门](AutoGen 入门.md)
