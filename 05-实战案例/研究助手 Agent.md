# 研究助手 Agent

> 自动完成信息搜集、整理、分析、报告生成。

---

## 场景描述

研究者需要深入了解某个主题：
1. 搜索相关信息
2. 阅读和理解内容
3. 整理和归纳
4. 生成研究报告

---

## 架构设计

```
研究主题 → 研究助手 Agent
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
 信息搜集   内容理解   报告生成
   工具       工具      工具
    │         │         │
    └─────────┼─────────┘
              ▼
         研究报告
```

---

## 工具定义

研究助手 Agent 需要六个工具来覆盖从信息搜集到报告生成的完整研究流程：

1. `search_web`：搜索网页，返回标题、摘要和链接
2. `read_article`：抓取网页正文内容（自动截断过长内容）
3. `search_papers`：通过 Semantic Scholar API 搜索学术论文
4. `summarize_content`：调用 LLM 总结内容，提取关键信息
5. `compare_sources`：对比两个信息源的异同，发现矛盾之处
6. `generate_report`：基于所有发现生成结构化研究报告

```python
from langchain.tools import tool
import requests
from bs4 import BeautifulSoup

@tool
def search_web(query: str, num_results: int = 5) -> str:
    """搜索网页，返回标题、摘要和链接"""
    # 使用搜索 API（示例用 Google Custom Search）
    results = search_api.search(query, num_results=num_results)
    
    output = []
    for i, result in enumerate(results, 1):
        output.append(f"{i}. {result['title']}")
        output.append(f"   摘要: {result['snippet']}")
        output.append(f"   链接: {result['link']}")
        output.append("")
    
    return "\n".join(output)

@tool
def read_article(url: str) -> str:
    """读取网页文章内容"""
    response = requests.get(url, timeout=10)
    soup = BeautifulSoup(response.text, 'html.parser')
    
    # 提取正文（简化处理）
    article = soup.find('article') or soup.find('main') or soup.find('body')
    text = article.get_text(separator='\n', strip=True)
    
    # 截断过长内容
    if len(text) > 5000:
        text = text[:5000] + "...(内容已截断)"
    
    return text

@tool
def search_papers(query: str, num_results: int = 5) -> str:
    """搜索学术论文"""
    # 使用 Semantic Scholar API
    url = f"https://api.semanticscholar.org/graph/v1/paper/search"
    params = {
        "query": query,
        "limit": num_results,
        "fields": "title,abstract,year,citationCount,url"
    }
    
    response = requests.get(url, params=params)
    papers = response.json().get("data", [])
    
    output = []
    for paper in papers:
        output.append(f"标题: {paper['title']}")
        output.append(f"年份: {paper.get('year', 'N/A')}")
        output.append(f"引用数: {paper.get('citationCount', 'N/A')}")
        output.append(f"摘要: {paper.get('abstract', 'N/A')[:200]}...")
        output.append(f"链接: {paper.get('url', 'N/A')}")
        output.append("")
    
    return "\n".join(output)

@tool
def summarize_content(content: str, focus: str = "") -> str:
    """总结内容，提取关键信息"""
    prompt = f"""请总结以下内容的关键信息：

{content}

{f"重点关注：{focus}" if focus else ""}

请用结构化格式输出：
1. 主要观点（3-5个）
2. 关键数据/事实
3. 重要结论"""
    
    return llm.call(prompt)

@tool
def compare_sources(source1: str, source2: str) -> str:
    """对比多个信息源的异同"""
    prompt = f"""请对比以下两个信息源：

信息源1：
{source1}

信息源2：
{source2}

请分析：
1. 共同点
2. 差异点
3. 矛盾之处
4. 综合结论"""
    
    return llm.call(prompt)

@tool
def generate_report(topic: str, findings: str) -> str:
    """生成研究报告"""
    prompt = f"""请基于以下研究发现，生成关于"{topic}"的研究报告：

{findings}

报告结构：
1. 摘要
2. 背景介绍
3. 主要发现
4. 详细分析
5. 结论与建议
6. 参考资料

要求：
- 专业、客观
- 有数据支撑
- 逻辑清晰"""
    
    return llm.call(prompt)
```

---

## Agent 实现

```python
from langchain.agents import AgentExecutor, create_openai_agent
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(model="gpt-4", temperature=0)

tools = [
    search_web, read_article, search_papers,
    summarize_content, compare_sources, generate_report
]

agent = create_openai_agent(llm, tools, prompt)

executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True,
    max_iterations=20
)

# 使用示例
result = executor.invoke({
    "input": "研究 2024 年大语言模型 Agent 领域的最新进展，生成一份研究报告"
})
```

---

## 使用示例

### 输入
```
研究 2024 年大语言模型 Agent 领域的最新进展，包括：
1. 主要技术突破
2. 代表性项目
3. 应用场景
4. 未来趋势
```

### Agent 执行流程

```
1. 调用 search_web("2024 LLM Agent 最新进展")
   → 返回相关文章列表

2. 调用 read_article(url1)
   → 读取文章内容

3. 调用 search_papers("LLM Agent 2024")
   → 返回学术论文列表

4. 调用 summarize_content(article1)
   → 总结关键信息

5. 调用 compare_sources(summary1, summary2)
   → 对比不同来源的信息

6. 调用 generate_report("LLM Agent 2024", findings)
   → 生成最终报告
```

### 输出
```
# 2024 年大语言模型 Agent 领域研究报告

## 摘要
2024年，LLM Agent 领域经历了快速发展，从概念验证走向实际应用...

## 1. 主要技术突破

### 1.1 工具使用能力增强
- GPT-4 Turbo 支持并行函数调用
- Claude 3 实现更精准的工具选择
- 开源模型（如 Qwen）工具能力大幅提升

### 1.2 长上下文处理
- Gemini 1.5 支持 100 万 token 上下文
- 实现更复杂的多步骤任务

### 1.3 多模态 Agent
- 结合视觉、语音、文本的多模态交互
- 如 Claude 3 的图像理解能力

## 2. 代表性项目

| 项目 | 特点 | 应用场景 |
|------|------|---------|
| AutoGen | 多 Agent 协作 | 研究、开发 |
| CrewAI | 角色扮演 | 团队协作 |
| LangGraph | 图结构工作流 | 复杂流程 |
| OpenClaw | 中文优化 | 本地部署 |

## 3. 应用场景
- 代码开发助手
- 数据分析自动化
- 客户服务
- 内容创作
- 科研辅助

## 4. 未来趋势
- Agent 标准化和互操作性
- 更强的推理和规划能力
- 安全性和可控性提升
- 成本降低和效率提升

## 参考资料
1. "The Rise of AI Agents" - Tech Review 2024
2. "LLM Agent Survey" - arXiv:2024.xxxxx
3. ...
```

---

## 扩展方向

1. **知识图谱**：构建领域知识图谱
2. **深度阅读**：支持 PDF、论文深度解析
3. **协作研究**：多个 Agent 分工研究
4. **持续跟踪**：自动跟踪领域最新动态
5. **引文管理**：自动生成参考文献

---

## 关键点

- **多源信息**：不要只依赖单一来源
- **信息验证**：交叉验证关键信息
- **结构化输出**：便于后续使用
- **引用来源**：保证可追溯性

---

**作者：** xuan