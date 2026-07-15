# Mini OpenHands Challenge

> 全栈 Agent 应用工程师面试编程题

## 题目

在 **1 天内**，构建一个 [OpenHands](https://github.com/All-Hands-AI/OpenHands) 的简化版本。

OpenHands 是一个开源的 AI 自主编程 Agent——你告诉它要做什么，它自己写代码、执行、看结果、修正错误，直到任务完成。

你的任务是深入理解它的架构思想，然后实现一个最小可用的简化版。

---

## 核心要求

构建一个 AI 编程 Agent，实现这个闭环：

```
用户描述编程任务 → Agent 理解并规划 → 生成代码 → 在沙箱中执行 → 观察结果 → 失败则自动修复 → 交付
```

你必须自己决定：

- 架构怎么设计
- Agent Loop 怎么实现
- LLM 怎么跟沙箱交互
- 前端怎么展示 Agent 的工作过程
- 哪些功能是 1 天内必须做的，哪些可以砍掉

---

## 必须实现

- 用户通过自然语言描述编程任务
- LLM 驱动的 Agent 自主完成代码生成与执行
- 代码在隔离环境中运行，不能直接触碰宿主机
- 执行失败时 Agent 能读取错误信息并尝试修复
- 前端实时展示 Agent 的思考过程和执行状态

---

## 技术栈

自由选择，只要你能在演示时解释清楚为什么这样选。

---

## 准备工作

1. 阅读 [OpenHands 源码](https://github.com/All-Hands-AI/OpenHands)
2. 实际运行体验：`docker run -d --name openhands -p 3000:3000 -e LLM_API_KEY=<your_key> -e LLM_MODEL=<model> ghcr.io/all-hands-ai/openhands`
3. 用 OpenHands 完成几个编程任务，理解用户视角的交互流程

---

## 提交

1. GitHub 公开仓库，包含完整可运行代码
2. README 需包含：项目简介、架构图、如何运行、技术选型理由、已知限制和下一步改进方向

---

## 演示（30 分钟）

- **5 分钟**：讲解你的架构设计，说明与 OpenHands 原版的差异和你的简化取舍
- **10 分钟**：走读核心代码，挑一个技术难点讲你的解决思路
- **10 分钟**：现场 Demo
- **5 分钟**：反思与问答

---

## 提示

- 1 天只够做核心链路。想清楚什么必须做，什么可以砍。
- System Prompt 可能是整个系统里最重要的组件。
- 安全性不能省——你的 Agent 在执行任意代码。

---

## License

MIT
