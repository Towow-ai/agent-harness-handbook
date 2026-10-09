# Agent Harness 手册

A Chinese-language handbook on agent harnesses: the layer around the model that lets long-running AI agent work be handed off, checked, and recovered. Its fifteen entries are mechanism write-ups from the Flowness project, each covering a real problem, the design that answers it, and where that design stops working.

这是一份关于 agent harness 的中文手册。agent harness 指模型之外、让长时间运行的 agent 工作能交接、能验收、能恢复的那一层。手册收录 ToWow 的 Flowness 项目里的 15 篇机制文章，每篇讲一个真实遇到的问题、采用的办法和它的边界。仓库只放目录和摘要，全文在 [towow.net](https://towow.net/articles?r=gh-c02)。

## 第 0 章：Agent Harness 是什么

agent harness 是包在模型外面的一层运行环境和规则，让一项要持续几天、换过几次会话、经过几个 agent 之手的工作，始终知道要做什么、做到了哪里、由谁判断做完。本章按六件事梳理这一层：任务与交接、上下文、权限与边界、事件与验收、调度与成本、恢复。随后列出它与模型 API、agent framework 的分界（两者有重叠，表格只按主要承担的事划分），并列出中文里现有的叫法：直接写 Agent Harness，或译作驾驭工程、挽具工程、约束系统工程，目前没有通行译法。正文各节链到讲它的那一篇；下面的目录按工作阶段另分四章。

[阅读第 0 章全文](https://towow.net/articles/what-is-agent-harness?r=gh-c02)

## 目录

### 第 1 章　工作怎样被交接

一项工作交给下一位时，最容易丢的是代码背后的决定。这一章讲需求、任务、上下文和验收条件怎样变成可接手的对象。

- [从需求到验收：Flowness 怎样把一项工作串起来](https://towow.net/articles/harness-workflow-architecture?r=gh-c02)　需求简报、任务包、上下文胶囊和提交信封连成一条交接链，复查发现的问题按原因退回负责的那一层。
- [长任务怎样交接、暂停与验收](https://towow.net/articles/harness-long-task-handover?r=gh-c02)　把任务、会话和成果当作三种状态。接班时核对什么，暂停时分哪几层，收货时看什么证据。
- [把上下文接到任务上：一次 Flowness 自开发实录](https://towow.net/articles/harness-self-development?r=gh-c02)　内核和检查都已实现，生产入口却填着占位引用。用范围内接受、范围外拒绝的正反控制验收。
- [上下文工作集：按阅读预算组织材料](https://towow.net/articles/harness-context-workset?r=gh-c02)　按执行、修复、审阅、规划的角色列出必读材料，分别记录哪些是原文交付，哪些只是审计投影。
- [访谈停止判定：需求信息是否足以进入设计](https://towow.net/articles/harness-interview-stop?r=gh-c02)　先看关键问题是否闭合，再看信息质量，给出足够、有条件足够或仍需补充的建议。
- [从第一个项目，长出企业自己的 Flowness 工作方法](https://towow.net/articles/harness-enterprise-growth?r=gh-c02)　企业的方法从一个真实项目开始，按需接入流程，交付成果的同时留下判断和检查依据。

### 第 2 章　怎样调度，怎样控制成本

任务变多、额度有限、失败会重复发生的时候，harness 要决定先做什么、做几个、什么时候停。

- [DAG 任务调度](https://towow.net/articles/harness-dag-dispatch?r=gh-c02)　先检查前置工作是否完成，再在可运行的任务里优先推进最长依赖链，避免并发数上去了项目仍卡在同一条链上。
- [预测配额调度](https://towow.net/articles/harness-quota-admission?r=gh-c02)　结合剩余额度、消耗速度和重置时间算出还能承受的并发，为在跑的工作留缓冲。
- [有界重派](https://towow.net/articles/harness-bounded-redispatch?r=gh-c02)　自动恢复有独立的次数预算，用尽后持久记录停止状态，交给需要改变条件的处理过程。
- [机器告警去重](https://towow.net/articles/harness-alert-dedup?r=gh-c02)　同一机器再次报告同一问题时，归入仍未解决的告警并记录复发；问题不同或原告警已结束，就重新提出。

### 第 3 章　怎样记账，怎样验收

工作历史越长，越要分清保存和阅读，也要分清“声明完成”和“证据齐全”。

- [增量事件索引](https://towow.net/articles/harness-event-index?r=gh-c02)　新事件只补进索引，查询命中后才展开正文，让不断增长的历史仍能按任务读取。
- [完成事实的入口：类型化事件与共享写入边界](https://towow.net/articles/harness-typed-events?r=gh-c02)　完成声明曾藏在通用事件的载荷里。修正把禁止包装的规则放进两条写路共用的校验边界。
- [从问题登记册到合并关口](https://towow.net/articles/harness-class-guards?r=gh-c02)　一次通过数足够、却漏掉守卫的“全绿”反例，以及随后的逐目标审计、合并后树验证和关口负对照。

### 第 4 章　怎样恢复，怎样拒绝过期的写入

进程会退出，连接会中断，被取代的执行者会在之后突然回来。

- [游标最后推进](https://towow.net/articles/harness-cursor-recovery?r=gh-c02)　沿着丢信号和重复计数两个故障窗口，推导游标、计数和字节水位的写入顺序。
- [世代号写入隔离](https://towow.net/articles/harness-generation-fencing?r=gh-c02)　租约到期后允许新执行者接手，存储端再核对世代号，拒绝旧执行者迟到的结果。

## 继续读

Flowness 真实问题系列讲多 agent 持续协作中反复出现的交接问题：[多 Agent 持续协作，哪些问题会在交接中反复出现](https://towow.net/articles/flowness-systemic-problems-guide?r=gh-c02)。

## 关于

ToWow，杭州和墨尔本。Flowness 是我们交付用的 agent harness，第 1 到 4 章的机制都来自做它的过程。

本手册的目录和摘要由 AI 辅助起草，依据 towow.net 已发表的文章整理，发布前做过事实核对。

联系：[towow.net/inquiry](https://towow.net/inquiry?r=gh-c02)
