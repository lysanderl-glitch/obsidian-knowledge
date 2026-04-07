# Harness Daily Publish SOP

## 系统概述

自动化博客发布系统，包含：
- 博客文章生成与发布
- 微信公众号草稿箱自动创建

## 架构

```
本地文件 → 发布脚本 → Astro构建 → Webhook → n8n → 微信草稿箱
```

## 组件

| 组件 | 路径/ID |
|------|---------|
| 发布脚本 | `/home/ubuntu/scripts/harness-daily-publish.sh` |
| 博客模板 | `/home/ubuntu/knowledge-base/📝-Articles/_article-template.astro.html` |
| 文章目录 | `/home/ubuntu/knowledge-base/📝-Articles/` |
| 输出目录 | `/home/ubuntu/website/src/pages/blog/` |
| n8n 工作流 | `LGkeWFUdYx5X7vgP` |
| Webhook | `https://n8n.lysander.bond/webhook/wechat-blog-draft` |

## 创建新文章

```bash
cd /home/ubuntu
./create-blog-article.sh "文章标题" "标签1,标签2"
```

## 手动发布

```bash
bash /home/ubuntu/scripts/harness-daily-publish.sh
```

## 定时任务

- **博客发布**: `harness-daily-publish.sh` 每天 22:30 迪拜时间
- **微信草稿**: n8n 工作流 `LGkeWFUdYx5X7vgP` 每天 22:45 迪拜时间

注意：n8n 使用 UTC 时区，22:45 迪拜时间 = 18:45 UTC

## METADATA 格式

```html
<!-- METADATA:{"title":"标题","date":"2026-04-07","tags":["标签1"],"description":"描述"} -->
```

**注意**：标题中的引号必须转义为 `\"`

## 常见错误

| 错误 | 原因 | 解决方案 |
|------|------|---------|
| JSON 解析失败 | 中文引号 `"` `"` 未转义 | 使用 `\"` |
| Layout 属性解析错误 | 双引号包裹含引号标题 | 模板使用单引号 `title='{{TITLE}}'` |
| esbuild 错误 | `<code>` 内 `{` 被当作 JS | 脚本自动转义为 `&#123;` |
| 微信定时触发晚 | 时区设置错误 | n8n cron 设为 `45 18 * * *` (UTC) |

## 验证

1. 检查博客：`https://lysander.bond/blog/`
2. 检查微信草稿箱：`https://mp.weixin.qq.com/draft`
3. 检查 n8n 工作流执行历史

## 关键文件

- `/home/ubuntu/.claude/projects/-home-ubuntu/memory/blog_article_system.md` - 系统标准
- `/home/ubuntu/.claude/projects/-home-ubuntu/memory/feedback_harness_self_healing.md` - 错误解决方案
