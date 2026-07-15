# Mini OpenHands Challenge

> 全栈 Agent 应用工程师面试编程题：一天内构建一个简化版 OpenHands

## 题目概述

[OpenHands](https://github.com/All-Hands-AI/OpenHands)（原名 OpenDevin）是当前最流行的开源 AI 自主编程 Agent 平台（GitHub 75,000+ Stars）。

本挑战要求你在 **1 天内** 深入理解 OpenHands 的架构设计，并实现一个 **最小可用简化版本**（MiniOpenHands），然后进行 **30 分钟演示**。

---

## 核心要求

构建一个 AI 编程 Agent，实现以下闭环：

```
用户输入编程任务 → Agent 理解任务 → LLM 生成代码/命令
    → 沙箱中执行 → 观察结果 → 如果失败则自动修复 → 返回最终结果
```

### 必须实现

| 序号 | 功能 | 说明 |
|------|------|------|
| 1 | **对话式任务输入** | 用户通过 Web UI 或 CLI 用自然语言描述编程任务 |
| 2 | **Agent 主循环** | Agent 循环：生成动作 → 执行 → 观察 → 判断是否继续 |
| 3 | **LLM 工具调用** | LLM 可以调用 `write_file` / `read_file` / `run_command` |
| 4 | **沙箱执行** | 代码在隔离环境中执行（subprocess 或 Docker） |
| 5 | **错误自动修复** | 执行失败时自动读取错误并重试修复（最多 3 轮） |
| 6 | **实时进度展示** | 前端实时展示 Agent 思考过程与工具调用（SSE 流式） |

### 加分项

| 序号 | 功能 |
|------|------|
| 7 | 文件工作区管理（多文件项目支持） |
| 8 | Git 自动提交 |
| 9 | 命令安全过滤（白名单/黑名单） |
| 10 | 会话持久化（刷新后可恢复） |
| 11 | 多 LLM Provider 支持 |

---

## 架构参考

```
┌──────────────────────────────────────────────────────┐
│                   前端 (Web UI)                       │
│  - 聊天输入框 / Agent 思考过程流式展示 / 终端输出       │
└──────────────────────┬───────────────────────────────┘
                       │ HTTP/SSE
┌──────────────────────▼───────────────────────────────┐
│                  后端 (FastAPI)                       │
│                                                       │
│  ┌──────────────────────────────────────────────┐    │
│  │            Agent Controller                   │    │
│  │  - 接收任务 / Agent Loop 主循环 / EventStream   │    │
│  └──────────────────┬───────────────────────────┘    │
│                     │                                  │
│  ┌──────────────────▼───────────────────────────┐    │
│  │              LLM Agent                        │    │
│  │  - System Prompt / Tool Calling / 错误修复     │    │
│  └──────────────────┬───────────────────────────┘    │
│                     │                                  │
│  ┌──────────────────▼───────────────────────────┐    │
│  │            Sandbox Runtime                    │    │
│  │  - subprocess/Docker / 文件管理 / 超时控制     │    │
│  └──────────────────────────────────────────────┘    │
└───────────────────────────────────────────────────────┘
```

### Agent Loop 伪代码

```python
async def agent_loop(task: str, workspace: Path, max_iterations: int = 10):
    messages = [{"role": "system", "content": SYSTEM_PROMPT},
                {"role": "user", "content": task}]

    for iteration in range(max_iterations):
        response = await llm.chat(messages, tools=TOOLS)
        tool_calls = response.get("tool_calls", [])

        if not tool_calls:  # LLM 认为任务完成
            yield {"type": "done", "content": response["content"]}
            break

        for tool_call in tool_calls:
            yield {"type": "tool_start", "tool": tool_call["name"]}
            observation = await sandbox.execute(tool_call)
            yield {"type": "tool_result", "output": observation}
            messages.append({"role": "assistant", "tool_calls": [tool_call]})
            messages.append({"role": "tool", "content": observation})
```

---

## 技术选型建议

| 层 | 推荐 | 备选 |
|---|------|------|
| 前端 | React / Vue3 + TypeScript | Streamlit |
| 后端 | Python FastAPI + SSE | Flask + WebSocket |
| LLM | OpenAI 兼容 API | 任意兼容接口 |
| 沙箱 | subprocess + 临时目录 | Docker SDK |
| 存储 | 文件系统 + JSON | SQLite |

---

## 演示要求（30 分钟）

### Part 1: 架构讲解（5 分钟）
- 画出系统架构图
- 解释 Agent Loop 的设计
- 说明与 OpenHands 原版的差异和简化取舍

### Part 2: 代码走读（10 分钟）
- Agent Loop 核心代码 (~50 行)
- 沙箱执行层实现
- LLM Tool Calling 的 Prompt 设计
- 一个技术难点的解决思路

### Part 3: 现场 Demo（10 分钟）

用以下 3 个递进任务现场演示：

> **任务 1（必须）**：写一个 Python 函数 `fibonacci(n)` 返回第 n 个斐波那契数并验证。
>
> **任务 2（必须）**：写一个 Python 脚本读取 CSV 文件，计算每列平均值。
>
> **任务 3（加分）**：写一个 Flask/FastAPI Web 服务，含 `/health` 和 `/echo/<message>` 端点。

### Part 4: 反思与问答（5 分钟）
- 如果给更多时间，你会改进什么？
- 最大挑战是什么？

---

## 评估标准

| 维度 | 权重 | 优秀 | 及格 | 不合格 |
|------|------|------|------|--------|
| **Agent 架构理解** | 25% | Agent Loop 清晰，错误恢复完善 | 基本跑通 | 不理解 Agent Loop |
| **代码质量** | 20% | 结构清晰，错误处理完善 | 能工作 | 硬编码，无错误处理 |
| **LLM 集成能力** | 15% | Tool Calling 合理，Prompt 有效 | 能调通 LLM | 无法调用 LLM |
| **沙箱安全** | 10% | 白名单/路径限制/超时/资源限制 | 基本隔离 | 直接在宿主机执行 |
| **前端体验** | 15% | 流式展示，思考过程可见 | 能展示结果 | 无前端 |
| **演示质量** | 15% | 3 个 demo 全跑通，讲解清楚 | 1-2 个 demo | Demo 失败 |

---

## 提交方式

1. GitHub 公开仓库，包含完整可运行代码
2. README 需包含：项目简介、架构图、运行说明、技术栈、已知限制
3. 面试前将仓库链接发给面试官

---

## 提示

- **不要试图复刻整个 OpenHands**。1 天只够做核心 Agent Loop + 3 个工具 + 基本 UI。做深不做广。
- **先跑通再优化**。第一版可以单轮，再逐步加入错误修复循环。
- **System Prompt 是关键**。花时间打磨，它决定了 Agent 的行为质量。
- **安全不能省**。至少要限制执行路径和命令超时。
- **演示多准备几个 case**，确保 demo 流畅。

---

## License

MIT
