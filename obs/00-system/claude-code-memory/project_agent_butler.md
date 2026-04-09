---
name: project_agent_butler_system
description: Lysander(CEO) + Butler团队(交付) + 多团队AI协作系统
type: project
---

## 当前系统架构

```
用户
  ↓ (以lysander开头)
Lysander (CEO管理层)
  ↓ 分解任务
├── Butler团队 (项目交付管理)
│   ├── 管家 (决策中枢)
│   ├── 交付专家
│   ├── 培训专家
│   ├── PMO专家
│   ├── IoT专家
│   ├── 建模专家
│   ├── IoT数据专家
│   └── UAT测试专家
├── 研发团队 (待集成)
└── OBS知识团队 (已集成)
    ├── obs_architecture_expert - 知识架构专家
    ├── knowledge_chandu_expert - 知识沉淀专家
    ├── knowledge_search_expert - 知识检索专家
    └── knowledge_quality_expert - 知识质量专家
```

## OBS Second Brain 2.0

**软件2.0 + Graphify 知识管理方案**

- 知识库路径: `/home/ubuntu/knowledge-base/obs`
- n8n workflows:
  - OBS Knowledge Intake (Kx1rPMUflImdl24B)
  - OBS Second Brain 2.0 Orchestrator (WLnMCb6SOAxM2V7Z)
- 状态: 架构已搭建，等待填充知识

## 唤起规则

**以 `lysander` 开头的消息**：唤起Lysander系统，作为专属沟通渠道

## 核心文件

| 文件 | 用途 |
|------|------|
| `/home/ubuntu/agent-butler/lysander.py` | Lysander主程序 |
| `/home/ubuntu/agent-butler/crew.py` | Butler团队主程序 |
| `/home/ubuntu/agent-butler/config/organization.yaml` | 组织架构配置 |
| `/home/ubuntu/agent-butler/config/experts.yaml` | 专家配置 |
| `/home/ubuntu/agent-butler/config/decision_rules.yaml` | 决策规则 |
| `/home/ubuntu/agent-butler/config/n8n_integration.yaml` | n8n集成 |

## 唤起方式

```bash
# 方式1: 通过Lysander (推荐)
/home/ubuntu/venv/crewai/bin/python /home/ubuntu/agent-butler/run.py "你的需求"

# 方式2: 直接调用Butler
/home/ubuntu/venv/crewai/bin/python /home/ubuntu/agent-butler/crew.py
```

## 决策系统

- **L1**: 自动执行（标准流程）
- **L2**: 管家批准后执行
- **L3**: 需上报用户决策

## 已完成Phase

- ✅ Phase 1: 核心框架
- ✅ Phase 2: 专家协作讨论
- ✅ Phase 3: 知识库 + 规则配置
- ✅ Phase 4: n8n自动化
- ✅ Phase 5: 用户审批界面
- ✅ Lysander: CEO管理层集成

**Why:** 系统已构建完成，支持多团队协作和CEO层管理
**How to apply:** 以lysander开头发送需求即可唤起系统
