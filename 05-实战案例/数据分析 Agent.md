# 数据分析 Agent

> 自动完成数据加载、清洗、分析、可视化全流程。

---

## 场景描述

用户有一份销售数据，需要：
1. 了解数据基本情况
2. 发现销售趋势和异常
3. 生成可视化图表
4. 输出分析报告

---

## 架构设计

```
用户请求 → 数据分析 Agent
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
 数据加载   数据处理   可视化
   工具       工具      工具
    │         │         │
    └─────────┼─────────┘
              ▼
         分析报告
```

---

## 工具定义

数据分析 Agent 需要四个工具来覆盖完整的分析流程：

1. `load_data`：加载 CSV 或 Excel 文件，返回数据的基本信息（行数、列名、数据类型、缺失值）
2. `analyze_data`：执行具体的分析操作（统计描述、分组聚合等）
3. `create_chart`：根据指定的图表类型生成可视化图表并保存为图片
4. `generate_report`：调用 LLM 把分析发现写成结构化报告

```python
from langchain.tools import tool
import pandas as pd
import matplotlib.pyplot as plt

@tool
def load_data(file_path: str) -> str:
    """加载 CSV 或 Excel 文件，返回数据概览"""
    if file_path.endswith('.csv'):
        df = pd.read_csv(file_path)
    else:
        df = pd.read_excel(file_path)
    
    info = {
        "行数": len(df),
        "列数": len(df.columns),
        "列名": list(df.columns),
        "数据类型": df.dtypes.to_dict(),
        "缺失值": df.isnull().sum().to_dict()
    }
    return str(info)

@tool
def analyze_data(query: str) -> str:
    """执行数据分析，支持：统计描述、分组聚合、趋势分析"""
    # 这里可以执行 pandas 操作
    # 简化示例
    result = df.describe()
    return result.to_string()

@tool
def create_chart(chart_type: str, x_col: str, y_col: str, title: str) -> str:
    """创建图表，支持：line, bar, scatter, pie"""
    plt.figure(figsize=(10, 6))
    
    if chart_type == "line":
        plt.plot(df[x_col], df[y_col])
    elif chart_type == "bar":
        plt.bar(df[x_col], df[y_col])
    elif chart_type == "scatter":
        plt.scatter(df[x_col], df[y_col])
    
    plt.title(title)
    plt.xlabel(x_col)
    plt.ylabel(y_col)
    
    chart_path = f"charts/{title}.png"
    plt.savefig(chart_path)
    plt.close()
    
    return f"图表已保存到: {chart_path}"

@tool
def generate_report(findings: str) -> str:
    """生成分析报告，包含发现和建议"""
    # 调用 LLM 生成报告
    prompt = f"基于以下发现，生成数据分析报告：\n{findings}"
    return llm.call(prompt)
```

---

## Agent 实现

```python
from langchain.agents import AgentExecutor, create_openai_agent
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(model="gpt-4", temperature=0)

tools = [load_data, analyze_data, create_chart, generate_report]

agent = create_openai_agent(llm, tools, prompt)

executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True,
    max_iterations=10
)

# 使用示例
result = executor.invoke({
    "input": "分析 sales.csv 文件，找出销售趋势，生成可视化图表和报告"
})
```

---

## 使用示例

### 输入
```
请分析这份销售数据，找出：
1. 每月销售趋势
2. 销售额最高的产品
3. 异常数据点
4. 改进建议
```

### Agent 执行流程

```
1. 调用 load_data("sales.csv")
   → 返回数据概览：1000行，8列

2. 调用 analyze_data("按月份统计销售额")
   → 返回月度销售统计

3. 调用 analyze_data("按产品统计销售额，取前10")
   → 返回 Top 10 产品

4. 调用 create_chart("line", "月份", "销售额", "月度销售趋势")
   → 保存趋势图

5. 调用 create_chart("bar", "产品", "销售额", "产品销售排名")
   → 保存排名图

6. 调用 generate_report("发现：3月销售异常高...")
   → 生成完整报告
```

### 输出
```
## 销售数据分析报告

### 1. 月度趋势
- 总体呈上升趋势
- 3月出现异常峰值（可能与促销活动相关）
- 7-8月为销售淡季

### 2. 产品分析
- Top 3 产品：A产品（35%）、B产品（25%）、C产品（15%）
- 长尾产品占比25%，建议优化库存

### 3. 异常发现
- 3月15日有一笔超大订单，需要确认是否真实

### 4. 建议
- 加强淡季营销
- 关注 Top 产品库存
- 调查异常订单

详见附件图表：charts/月度销售趋势.png, charts/产品销售排名.png
```

---

## 扩展方向

1. **实时数据**：连接数据库，实时分析
2. **多维分析**：支持更复杂的维度组合
3. **预测功能**：基于历史数据预测未来趋势
4. **异常检测**：自动发现数据异常
5. **报告模板**：支持自定义报告格式

---

## 关键点

- **工具粒度**：每个工具职责单一
- **错误处理**：数据格式异常时有明确提示
- **结果缓存**：避免重复计算
- **可视化**：图表比文字更直观

---

**作者：** xuan