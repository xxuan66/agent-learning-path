# Agent 应用场景

Agent 的价值不在于“哪里都能用”，而在于：在某些需要多步判断、外部工具、持续上下文和动态反馈的场景里，它确实比单轮问答更有优势。

所以看应用场景时，最重要的问题不是“能不能接入 Agent”，而是：

- 这个任务是否真的需要 Agent
- Agent 引入后能否带来明显收益
- 是否值得承担额外复杂度

## 🏢 企业应用

### 1. 智能客服

**场景描述**：7x24 小时自动回答客户问题，处理常见咨询

**核心功能**：
- 自然语言理解
- 知识库检索
- 问题分类和路由
- 情绪识别和升级

**技术栈**：

下面这段代码展示了一个客服 Agent 的内部结构。它有三个核心组件：知识库（用向量数据库存储产品文档）、工单系统（连接售后 API）和情绪分析器（判断用户是否生气）。

```python
# 客服 Agent 示例架构
class CustomerServiceAgent:
    def __init__(self):
        self.knowledge_base = VectorStore("product_docs")
        self.ticket_system = TicketAPI()
        self.sentiment_analyzer = SentimentModel()
    
    def handle_query(self, query, user_context):
        # 理解问题
        intent = self.classify_intent(query)
        
        # 检索知识
        answer = self.knowledge_base.search(query)
        
        # 检查情绪
        if self.sentiment_analyzer.is_angry(query):
            self.escalate_to_human(user_context)
        
        return answer
```

**效益**：
- 响应时间从小时级降至秒级
- 人力成本降低 60-80%
- 客户满意度提升 30%

**适合 Agent 的原因：**

- 需要持续上下文
- 需要知识检索
- 需要路由和升级处理

### 2. 数据分析助手

**场景描述**：自动分析业务数据，生成洞察报告

**功能示例**：
- 销售数据分析
- 用户行为分析
- 市场趋势预测
- 异常检测

下面的代码展示了如何用 LangChain 快速创建一个能分析 DataFrame 的 Agent。它本质上是让大模型直接操作 pandas 表格——你说"上个月销售额最高的产品"，它自动写出对应的 pandas 代码并执行。

```python
# 数据分析 Agent
from langchain.agents import create_pandas_dataframe_agent

df_agent = create_pandas_dataframe_agent(
    llm,
    df,
    verbose=True
)

df_agent.run("上个月销售额最高的产品是什么？")
df_agent.run("分析用户流失的原因")
df_agent.run("生成销售趋势图表")
```

### 3. 文档处理自动化

**场景**：合同审核、报告生成、内容摘要

下面的代码展示了一个文档处理 Agent 的两个核心功能：

1. `review_contract`：自动提取合同中的关键条款，检测潜在风险，生成审核意见
2. `summarize_report`：从长报告中提取关键信息，生成精简摘要

```python
class DocumentAgent:
    def review_contract(self, contract_text):
        # 提取关键条款
        clauses = self.extract_clauses(contract_text)
        
        # 风险检测
        risks = self.detect_risks(clauses)
        
        # 生成审核意见
        return self.generate_review(risks)
    
    def summarize_report(self, report):
        # 提取关键信息
        key_points = self.extract_key_points(report)
        
        # 生成摘要
        summary = self.llm.summarize(key_points)
        
        return summary
```

## 👤 个人应用

### 1. 个人助手

**功能**：
- 日程管理
- 邮件处理
- 信息检索
- 任务提醒

下面的代码展示了个人助手 Agent 的"晨间简报"功能。它同时连接日历 API、邮件 API 和任务管理器，把今日信息汇总成一段话发给用户——这比用户自己打开三个 App 查看高效得多。

```python
# 个人助手 Agent
class PersonalAssistant:
    def __init__(self):
        self.calendar = CalendarAPI()
        self.email = EmailAPI()
        self.tasks = TaskManager()
    
    def morning_briefing(self):
        """生成晨间简报"""
        events = self.calendar.get_today_events()
        emails = self.email.get_urgent()
        tasks = self.tasks.get_pending()
        
        briefing = f"""
        早上好！今日安排：
        
        📅 日程：{len(events)} 个会议
        📧 邮件：{len(emails)} 封紧急
        ✅ 待办：{len(tasks)} 项任务
        
        详细安排：
        {self.format_events(events)}
        """
        return briefing
```

### 2. 学习助手

**功能**：
- 知识点讲解
- 习题解答
- 学习规划
- 进度跟踪

### 3. 创作助手

**功能**：
- 文章写作
- 代码生成
- 创意构思
- 内容润色

## 🔬 专业领域

### 1. 医疗辅助

**应用**：
- 症状初步分析
- 医学文献检索
- 用药建议查询
- 健康档案管理

**注意事项**：
⚠️ 仅作为辅助工具，不能替代专业医疗建议

### 2. 法律支持

**应用**：
- 法律条文检索
- 案例相似性分析
- 合同条款审查
- 法律文书起草

### 3. 教育辅助

**应用**：
- 个性化教学
- 作业批改
- 答疑辅导
- 学习资源推荐

## 🛠️ 开发者工具

### 1. 编程助手

**功能**：
- 代码生成
- Bug 修复
- 代码审查
- 文档生成

下面的代码展示了编程 Agent 的两个核心能力：

1. `generate_code`：根据自然语言描述生成代码。你只需说"实现一个排序算法"，它就能写出完整代码
2. `debug_code`：把出错的代码和错误信息一起发给它，它会分析原因并给出修复方案

```python
# 编程 Agent 示例
class CodingAgent:
    def generate_code(self, requirement):
        """根据需求生成代码"""
        prompt = f"""
        请编写{requirement}的代码
        
        要求:
        - 遵循最佳实践
        - 添加注释
        - 包含错误处理
        """
        return self.llm.generate(prompt)
    
    def debug_code(self, code, error_message):
        """调试代码"""
        prompt = f"""
        以下代码出现错误：
        
        代码：{code}
        错误：{error_message}
        
        请分析原因并修复。
        """
        return self.llm.generate(prompt)
```

### 2. 测试自动化

**功能**：
- 测试用例生成
- 自动化测试执行
- Bug 报告生成

### 3. DevOps 助手

**功能**：
- CI/CD 流程优化
- 日志分析
- 故障诊断
- 资源监控

## 🌐 新兴应用

## 一个更实用的判断标准

如果一个场景同时满足下面几条，通常更值得考虑 Agent：

1. 任务不是一步完成的
2. 需要访问外部系统或工具
3. 执行中需要根据结果继续调整
4. 任务本身有明显上下文连续性

如果只是简单生成、改写、分类，很多时候并不需要引入 Agent。

### 1. 多 Agent 协作

多个 Agent 协同完成复杂任务：

```
研究团队 Agent 架构：
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  搜索 Agent  │ →  │  分析 Agent  │ →  │  写作 Agent  │
└─────────────┘    └─────────────┘    └─────────────┘
       ↓                  ↓                  ↓
  收集资料           整理分析           生成报告
```

### 2. 具身智能

Agent 与物理世界交互：
- 机器人控制
- 智能家居
- 自动驾驶

### 3. 游戏 NPC

- 智能对话
- 行为决策
- 剧情生成

## 📊 应用选择指南

| 场景复杂度 | 推荐方案 | 技术难度 |
|------------|----------|----------|
| 简单问答 | 单 Agent + 知识库 | ⭐ |
| 任务执行 | 单 Agent + 工具 | ⭐⭐ |
| 复杂流程 | 多 Agent 协作 | ⭐⭐⭐ |
| 实时交互 | Agent + 流式处理 | ⭐⭐⭐ |
| 跨系统 | Agent + API 集成 | ⭐⭐⭐⭐ |

## 🚀 开始你的第一个 Agent

```python
# 5 分钟快速上手
# 这段代码展示了创建一个最简 Agent 的完整过程：
# 1. 定义一个加法工具（add 函数）
# 2. 创建 GPT-4 模型实例
# 3. 组装成 Agent
# 4. 用自然语言提问，Agent 自动调用 add 工具得到结果

from langchain_openai import ChatOpenAI
from langchain.agents import AgentExecutor, create_tool_calling_agent
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.tools import tool

@tool
def add(a: int, b: int) -> int:
    """两个数相加"""
    return a + b

# 创建 LLM
llm = ChatOpenAI(model="gpt-4", temperature=0)

# 创建提示词模板
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个数学助手"),
    ("human", "{input}"),
])

# 创建 Agent
tools = [add]
agent = create_tool_calling_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

# 使用 Agent
result = agent_executor.invoke({"input": "123 + 456 = ?"})
print(result["output"])
```

## 📚 案例研究

详见 [05-实战案例](../05-实战案例/README.md)

---

**上一章**: [Agent 核心能力](Agent 核心能力.md) | **下一章**: [02-技术演进](../02-技术演进/README.md)
