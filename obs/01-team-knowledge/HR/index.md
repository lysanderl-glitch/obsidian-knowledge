---
title: 人力资源知识库
description: 公司AI Agent团队、人员、岗位、能力管理体系
type: HR-index
version: "1.0"
created: 2026-04-09
---

# 人力资源知识库

公司AI Agent团队、人员、岗位、能力管理的核心知识库。OBS作为单一数据源，为Lysander CEO提供团队召唤能力。

## 重要说明

**全员AI团队**：所有团队成员均为AI Agent，由Lysander(人类CEO)统一管理。

---

## 目录结构

```
HR/
├── personnel/           # 人员档案库
│   ├── lysander/       # CEO (人类)
│   ├── butler/         # Butler团队 (7 AI Agents)
│   ├── obs/            # OBS知识管理团队 (4 AI Agents)
│   ├── content_ops/    # 内容运营团队 (4 AI Agents)
│   └── rd/             # 研发团队 (5 AI Agents)
└── positions/          # 岗位定义库
    ├── lysander/
    └── rd/
```

## 团队概览

| 团队 | 负责人 | 成员数 | 状态 |
|------|--------|--------|------|
| lysander | - | 1 | active (人类) |
| butler | Lysander | 7 | active (AI) |
| obs | Lysander | 4 | active (AI) |
| content_ops | Lysander | 4 | active (AI) |
| rd | Lysander | 5 | active (AI) |

---

## AI团队架构

```
┌─────────────────────────────────────────────────────────────┐
│                      Lysander (CEO - 人类)                    │
│  - 战略方向                                                  │
│  - 资源协调                                                  │
│  - 最终决策                                                  │
│  - 对用户负责                                                │
└─────────────────────────────────────────────────────────────┘
                              ↑
         ┌────────────────────┼────────────────────┐
         │                    │                    │
         ↓                    ↓                    ↓
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│ Butler团队    │    │ RD团队        │    │ OBS团队       │
│ (7 AI Agents) │    │ (5 AI Agents) │    │ (4 AI Agents) │
├───────────────┤    ├───────────────┤    ├───────────────┤
│ delivery_expert│   │ tech_lead    │    │obs_architecture│
│ training_expert│   │ backend_dev  │    │knowledge_chandu│
│ pmo_expert    │    │ frontend_dev │    │knowledge_search│
│ iot_expert    │    │ devops_eng   │    │knowledge_quality│
│ digital_modeling│   │ qa_engineer  │    └───────────────┘
│ iot_data_expert│   └───────────────┘
│ uat_test_expert│           ↑
└───────────────┘            │
                              │
              ┌───────────────┴───────────────┐
              │        Content_ops团队         │
              │        (4 AI Agents)           │
              ├───────────────────────────────┤
              │content_strategist              │
              │content_creator                 │
              │visual_designer                 │
              │publishing_ops                  │
              └───────────────────────────────┘
```

---

## 召唤规则

| 任务关键词 | 路由团队 | 路由专家 |
|-----------|----------|----------|
| 交付、项目、IoT | butler | - |
| 知识库、OBS | obs | - |
| 内容、博客、微信 | content_ops | - |
| 研发、开发、架构 | rd | tech_lead |
| 前端 | rd | frontend_dev |
| 后端 | rd | backend_dev |
| DevOps、部署 | rd | devops_engineer |
| 测试 | rd | qa_engineer |
| 管理、战略 | lysander | lysander |

---

## 专家调用流程

```
用户需求
    ↓
Lysander接收 → 分析 → 路由到对应团队AI Agent
    ↓
AI Agent执行任务
    ↓
结果汇报 → Lysander汇总 → 用户
```

---

## 更新日志

- 2026-04-09: 初始化全员AI团队HR知识库
