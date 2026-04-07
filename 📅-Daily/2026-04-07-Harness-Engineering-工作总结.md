# Harness Engineering 体系化工作总结

> 日期：2026-04-07
> 作者：Lysander

---

## 一、项目概述

Harness Engineering 是一项针对 AI Agent（Claude Code）运行时错误的智能化分析与自愈闭环系统。

**核心理念：** 当 AI 在执行任务过程中遇到错误时，系统能够自动分析根因、给出修复建议，并根据置信度决定是自动执行还是通知人工。

---

## 二、问题背景

在使用 Claude Code 日常开发过程中会遇到各种错误：
- npm 安装失败（ENOENT、EINTEGRITY）
- Git 合并冲突
- Docker 构建错误
- 权限/网络问题

**传统做法：** 人工排查、日志分析、搜索引擎查询，耗时且低效。

**目标：** 让 AI 能够自主分析错误、给出修复建议，并在高置信度场景下自动执行修复。

---

## 三、技术架构

### 3.1 核心组件

```
错误发生 → Claude Code / Webhook → n8n Workflow → Python分析服务器 → Claude API
                                              ↓
                                        结构化分析结果
                                              ↓
                                    Slack通知 / 自动执行
```

**组件说明：**

| 组件 | 技术选型 | 作用 |
|------|---------|------|
| 分析服务器 | Python HTTP Server | 调用 Claude API 进行错误分析 |
| 工作流引擎 | n8n | Webhook 路由、Slack 通知 |
| AI Provider | Claude API (MiniMax 代理) | 提供错误分析和决策能力 |
| CLI 命令 | `/analyze-error` | Claude Code 内直接分析错误 |

### 3.2 关键技术创新

**MiniMax API 响应格式适配：**
- MiniMax-M2 模型返回 `thinking` + `text` 混合结构
- 需添加 `"thinking": {"type": "disabled"}` 获取纯净文本响应
- 需要遍历 `content` 数组找到 `type: "text"` 元素

**AI 输出增强：**
```json
{
  "analysis": "根因分析",
  "suggested_fix": "人类可读修复步骤",
  "confidence": 0.95,
  "auto_action": {
    "can_auto_execute": true,
    "action_type": "cleanup",
    "action_params": {"target": "npm_cache"}
  }
}
```

---

## 四、实施成果

### 4.1 已上线功能

| 功能 | 状态 | 说明 |
|------|------|------|
| Webhook 接口 | ✅ | `https://n8n.lysander.bond/webhook/harness-analyze` |
| Python 分析服务器 | ✅ | port 5810, systemd 管理 |
| `/analyze-error` 命令 | ✅ | Claude Code 内直接使用 |
| Slack 通知 | ✅ | 消息推送到 #所有-lysander |
| 自动执行框架 | ✅ | cleanup/notify/retry 动作 |
| 定时收集任务 | ✅ | 每天 23:00 迪拜时间 |

### 4.2 使用示例

```bash
# CLI 使用
/analyze-error "npm ERR! code ENOENT"

/# Webhook 调用
curl -X POST https://n8n.lysander.bond/webhook/harness-analyze \
  -H "Content-Type: application/json" \
  -d '{"error_message": "npm ERR! code ENOENT", "context": "docker build", "source": "cli"}'
```

### 4.3 输出示例

```
🔍 Harness Error Analysis
━━━━━━━━━━━━━━━━━━━━━━━━

📋 Error: npm ERR! code EINTEGRITY verification failed

📊 Analysis: ✅ (95% confidence)
   npm integrity verification failed due to corrupted cache...

🔧 Suggested Fix:
   1. npm cache clean --force
   2. rm -rf node_modules package-lock.json
   3. npm install

📁 Category: network
🎯 Action: 🤖 Auto-executable (cleanup)
━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 五、置信度与行动矩阵

| 置信度 | 行动 | 人工介入 |
|--------|------|----------|
| ≥90% | 自动执行 | 无 |
| 70-89% | 执行 + 监控 | 失败则介入 |
| 50-69% | 执行 + 全程可见 | 全程可见 |
| <50% | 通知人工 | 必须介入 |

---

## 六、知识积累机制

### 6.1 自动收集

- **定时任务**：每天 23:00 扫描会话日志
- **来源**：`/home/ubuntu/.claude/projects/-home-ubuntu/*.jsonl`
- **流程**：提取错误 → AI 分析 → 记录到知识库

### 6.2 知识库结构

```
knowledge-base/
└── 4-🛡️-Harness错误库/
    ├── errors/
    │   └── 2026-04.md    # 月度错误日志
    ├── _index.md          # 索引
    └── README.md          # 说明文档
```

---

## 七、调试经验总结

### 问题1：API 返回空 analysis
- **根因**：MiniMax API 的 `thinking` 模式返回混合内容
- **解决**：添加 `"thinking": {"type": "disabled"}`

### 问题2：JSON 响应被截断
- **根因**：`max_tokens=300` 不足
- **解决**：增加到 `max_tokens=500` 以上

### 问题3：端口占用
- **解决**：使用 `socketserver.TCPServer.allow_reuse_address = True`

---

## 八、后续计划

### Phase 1 ✅ 完成
- 分析 + 建议 + Slack 通知
- 积累真实错误数据

### Phase 2 🔄 框架就绪
- 高置信度(≥90%) 自动执行
- 需要真实数据校准置信度

### Phase 3 ⏳ 待启动
- 逐步降低置信度阈值
- 扩大自动执行范围

---

## 九、可封装性分析

### 9.1 核心模块

| 模块 | 可移植性 | 说明 |
|------|---------|------|
| Python 分析服务器 | ✅ 高 | 纯 Python，依赖少 |
| n8n Workflow | ✅ 高 | JSON 导出/导入 |
| `/analyze-error` 命令 | ✅ 高 | Shell 脚本，跨平台 |
| 定时收集脚本 | ✅ 高 | Bash + jq |

### 9.2 封装方式

**方案A：Docker Compose 一键部署**
```yaml
services:
  harness-analyze:
    build: ./harness-analyze
    ports:
      - "5810:5810"
  harness-n8n:
    image: n8n
    volumes:
      - ./workflows:/workflows
```

**方案B：独立部署脚本**
```bash
curl -fsSL https://example.com/install.sh | bash
```

---

## 十、文件清单

| 文件 | 路径 | 说明 |
|------|------|------|
| 分析服务器 | `/home/ubuntu/scripts/harness-analyze-server.py` | Python HTTP Server |
| CLI 命令 | `/home/ubuntu/.claude/commands/analyze-error.sh` | Claude Code 命令 |
| 收集脚本 | `/home/ubuntu/scripts/harness-collect-errors.sh` | 定时任务 |
| n8n Workflow | `Qr4a3oJper1dLxuZ` | Webhook + 分析 + 通知 |
| 方案文档 | `/home/ubuntu/knowledge-base/Harness-AI-Driven-Self-Healing-闭环方案.md` | 完整方案 |

---

## 十一、结论

Harness Engineering 成功将 AI 错误分析从"人工排查"转变为"AI 驱动"模式：

1. **效率提升**：错误分析从分钟级降到秒级
2. **知识积累**：自动收集、分析、记录，形成错误知识库
3. **持续进化**：置信度机制确保系统越用越准

**核心价值：** 不是替代人工，而是放大人工能力——让 AI 处理重复性分析工作，人专注于创造性任务。
