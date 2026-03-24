# 客服助手

构建一个智能客服 Agent。

## 🎯 需求分析

**功能需求**：
- 自动回答常见问题
- 查询订单状态
- 处理退换货请求
- 转接人工客服

**技术需求**：
- 知识库检索
- API 集成
- 对话管理
- 情绪识别

## 🏗️ 系统架构

```
┌─────────────────────────────────────────┐
│           客服 Agent 系统                 │
├─────────────────────────────────────────┤
│                                          │
│  ┌─────────────┐    ┌─────────────┐     │
│  │  知识库检索  │    │  订单查询    │     │
│  └──────┬──────┘    └──────┬──────┘     │
│         │                  │             │
│         └────────┬─────────┘             │
│                  │                       │
│           ┌──────▼──────┐                │
│           │  LLM 核心    │                │
│           └──────┬──────┘                │
│                  │                       │
│         ┌────────┼────────┐              │
│         │        │        │              │
│  ┌──────▼──┐ ┌──▼────┐ ┌─▼──────┐       │
│  │情绪分析  │ │对话管理│ │人工转接 │       │
│  └─────────┘ └───────┘ └────────┘       │
│                                          │
└─────────────────────────────────────────┘
```

## 💻 实现代码

```python
from langchain.agents import create_agent
from langchain.tools import tool
from langchain.vectorstores import Chroma
from langchain.memory import ConversationBufferMemory
from langchain_openai import ChatOpenAI

# 初始化
llm = ChatOpenAI(model="gpt-4", temperature=0.7)
memory = ConversationBufferMemory(return_messages=True)

# 知识库
knowledge_base = Chroma(
    embedding_function=embeddings,
    persist_directory="./customer_service_kb"
)

@tool
def search_knowledge(query: str) -> str:
    """从知识库搜索产品信息和常见问题"""
    results = knowledge_base.similarity_search(query, k=3)
    return "\n".join([r.page_content for r in results])

@tool
def check_order_status(order_id: str) -> str:
    """查询订单状态"""
    # 调用订单 API
    response = requests.get(f"/api/orders/{order_id}")
    order = response.json()
    return f"订单{order_id}: {order['status']}, 预计{order['eta']}送达"

@tool
def process_return(order_id: str, reason: str) -> str:
    """处理退换货请求"""
    # 创建退换货工单
    ticket = create_return_ticket(order_id, reason)
    return f"已创建退换货工单{ticket.id}，客服将尽快处理"

@tool
def escalate_to_human(context: str) -> str:
    """转接人工客服"""
    ticket = create_human_ticket(context)
    return f"已转接人工客服，工单号{ticket.id}，预计 5 分钟内响应"

# 创建 Agent
agent = create_agent(
    llm=llm,
    tools=[
        search_knowledge,
        check_order_status,
        process_return,
        escalate_to_human
    ],
    memory=memory,
    system_message="""你是一个专业的客服助手。

工作流程：
1. 先理解用户问题
2. 产品问题查询知识库
3. 订单问题查询订单系统
4. 复杂问题创建工单
5. 愤怒用户转接人工

语气要求：
- 友好、专业、耐心
- 使用敬语
- 表达同理心
"""
)

# 使用示例
def handle_customer_query(query: str):
    response = agent.invoke({
        "messages": [{"role": "user", "content": query}]
    })
    return response
```

## 📊 效果优化

### 1. 知识库建设

```python
def build_knowledge_base():
    """构建知识库"""
    docs = []
    
    # 加载产品文档
    docs.extend(load_product_docs())
    
    # 加载 FAQ
    docs.extend(load_faq())
    
    # 加载历史对话
    docs.extend(load_successful_conversations())
    
    # 存入向量库
    knowledge_base.add_documents(docs)
```

### 2. 情绪识别

```python
from transformers import pipeline

sentiment_analyzer = pipeline("sentiment-analysis")

def detect_emotion(text: str) -> str:
    result = sentiment_analyzer(text)[0]
    return result["label"]  # POSITIVE/NEGATIVE/NEUTRAL

# 在 Agent 中使用
if detect_emotion(user_query) == "NEGATIVE":
    # 优先转接人工
    escalate_to_human.invoke(user_query)
```

### 3. 对话质量监控

```python
def evaluate_response(query: str, response: str) -> dict:
    """评估回复质量"""
    return {
        "relevance": llm.evaluate(f"回复是否相关：{query} -> {response}"),
        "helpfulness": llm.evaluate(f"回复是否有帮助：{response}"),
        "tone": llm.evaluate(f"语气是否合适：{response}")
    }
```

## 📈 关键指标

| 指标 | 目标值 | 测量方法 |
|------|--------|----------|
| 解决率 | >80% | 无需人工介入 |
| 响应时间 | <2 秒 | 平均响应时间 |
| 满意度 | >4.5/5 | 用户评分 |
| 转接率 | <20% | 转人工比例 |

## 🚀 部署建议

```python
# FastAPI 部署
from fastapi import FastAPI

app = FastAPI()

@app.post("/api/customer-service")
async def customer_service(query: str):
    response = handle_customer_query(query)
    return {"reply": response}
```

---

**上一章**: [05-实战案例](README.md) | **下一章**: [数据分析助手](数据分析助手.md)
