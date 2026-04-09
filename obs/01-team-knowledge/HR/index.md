---
title: 人力资源知识库
description: 公司人员、岗位、能力管理体系
type: HR-index
version: "1.0"
created: 2026-04-09
---

# 人力资源知识库

公司人员、岗位、能力管理的核心知识库。OBS作为单一数据源，为Lysander CEO提供团队召唤能力。

## 目录结构

```
HR/
├── personnel/           # 人员档案库
│   ├── lysander/       # CEO
│   ├── butler/         # Butler团队
│   ├── obs/            # OBS知识管理团队
│   ├── content_ops/    # 内容运营团队
│   └── rd/             # 研发团队
└── positions/          # 岗位定义库
    ├── lysander/
    ├── butler/
    ├── obs/
    └── rd/
```

## 团队概览

| 团队 | 负责人 | 成员数 | 状态 |
|------|--------|--------|------|
| lysander | - | 1 | active |
| butler | TBD | 7 | partial |
| obs | TBD | 4 | partial |
| content_ops | TBD | 4 | partial |
| rd | TBD | 5 | pending |

## 人员档案索引

### lysander 团队
- [[lysander]] - 公司CEO/管理者

### butler 团队
- [[butler/delivery_expert]] - 全球交付专家
- [[butler/training_expert]] - 培训专家
- [[butler/pmo_expert]] - PMO专家
- [[butler/iot_expert]] - IoT施工专家
- [[butler/digital_modeling_expert]] - 数字化建模专家
- [[butler/iot_data_expert]] - IoT数据专家
- [[butler/uat_test_expert]] - UAT测试专家

### obs 团队
- [[obs/obs_architecture_expert]] - 知识架构专家
- [[obs/knowledge_chandu_expert]] - 知识沉淀专家
- [[obs/knowledge_search_expert]] - 知识检索专家
- [[obs/knowledge_quality_expert]] - 知识质量专家

### content_ops 团队
- [[content_ops/content_strategist]] - 内容策划专家
- [[content_ops/content_creator]] - 内容创作专家
- [[content_ops/visual_designer]] - 视觉设计专家
- [[content_ops/publishing_ops]] - 发布运营专家

### rd 团队
- [[rd/tech_lead]] - 技术负责人
- [[rd/backend_dev]] - 后端开发工程师
- [[rd/frontend_dev]] - 前端开发工程师
- [[rd/devops_engineer]] - DevOps工程师
- [[rd/qa_engineer]] - QA测试工程师

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

## 更新日志

- 2026-04-09: 初始化HR知识库框架
