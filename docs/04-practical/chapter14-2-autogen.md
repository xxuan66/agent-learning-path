# AutoGen 入门

AutoGen 是微软推出的多 Agent 协作框架。

## 📦 安装

```bash
pip install pyautogen
```

## 🎯 核心概念

### ConversableAgent（可对话 Agent）

```python
from autogen import ConversableAgent

# 创建 Agent
agent = ConversableAgent(
    name="助手",
    system_message="你是一个有帮助的 AI 助手",
    llm_config={"config_list": [{"model": "gpt-4", "api_key": "your-key"}]}
)

# 发起对话
response = agent.generate_reply(
    messages=[{"role": "user", "content": "你好"}]
)
```

### UserProxyAgent（用户代理）

```python
from autogen import UserProxyAgent

# 创建用户代理（可执行代码）
user_proxy = UserProxyAgent(
    name="用户",
    human_input_mode="TERMINATE",  # 何时请求人类输入
    max_consecutive_auto_reply=10,  # 最大自动回复数
    code_execution_config={"work_dir": "coding"}
)
```

## 🤝 多 Agent 协作

### 双 Agent 对话

```python
from autogen import ConversableAgent

# 创建两个 Agent
coder = ConversableAgent(
    name="Coder",
    system_message="你是程序员，负责写代码",
    llm_config={"config_list": [{"model": "gpt-4"}]}
)

reviewer = ConversableAgent(
    name="Reviewer",
    system_message="你是代码审查员，负责检查代码",
    llm_config={"config_list": [{"model": "gpt-4"}]}
)

# 启动对话
coder.initiate_chat(
    reviewer,
    message="请帮我审查这段代码：print('hello')"
)
```

### 群聊模式

```python
from autogen import GroupChat, GroupChatManager

# 创建群聊
groupchat = GroupChat(
    agents=[coder, reviewer, tester],
    messages=[],
    max_round=10
)

# 创建群聊管理员
manager = GroupChatManager(
    groupchat=groupchat,
    llm_config={"config_list": [{"model": "gpt-4"}]}
)

# 启动群聊
user_proxy.initiate_chat(
    manager,
    message="我们一起开发一个计算器应用"
)
```

## 🛠️ 工具使用

### 注册函数作为工具

```python
from autogen import register_function

def get_weather(city: str) -> str:
    """获取天气"""
    return f"{city} 今天晴朗"

# 注册工具
register_function(
    get_weather,
    caller=coder,
    executor=user_proxy,
    name="get_weather",
    description="查询城市天气"
)
```

## 📊 对话模式

| 模式 | 描述 | 适用场景 |
|------|------|----------|
| TERMINATE | 用户决定何时结束 | 需要人类控制 |
| NEVER | 永不请求人类输入 | 完全自动化 |
| ALWAYS | 每次都请求人类输入 | 高监督场景 |

## 📚 学习资源

- [官方文档](https://microsoft.github.io/autogen/)
- [GitHub](https://github.com/microsoft/autogen)

---

**上一章**: [LangChain 入门](chapter14-1-langchain.md) | **下一章**: [CrewAI 入门](chapter14-3-crewai.md)
