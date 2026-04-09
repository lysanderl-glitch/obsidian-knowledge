# Astro 博客文章发布系统

## 系统组成
- **模板**: `/home/ubuntu/knowledge-base/📝-Articles/_article-template.astro.html`
- **创建脚本**: `/home/ubuntu/scripts/create-blog-article.sh`
- **发布脚本**: `/home/ubuntu/scripts/harness-daily-publish.sh`
- **输出目录**: `/home/ubuntu/website/src/pages/blog/`

## 关键规范

### 发布流程（必须遵循）

**禁止直接cp复制文件到website目录！**

必须使用发布脚本：
```bash
bash /home/ubuntu/scripts/harness-daily-publish.sh
```

原因：直接cp会导致特殊字符编码问题，构建失败。

### 创建新文章
```bash
./create-blog-article.sh "文章标题" "标签1,标签2"
```

## METADATA 格式
```html
<!-- METADATA:{"title":"标题","date":"2026-04-07","tags":["标签1","标签2"],"description":"描述"} -->
```

**注意**: 标题中的引号必须转义为 `\"`
- 错误: `"人工排查"`
- 正确: `\"人工排查\"`

## 模板关键规范

### 1. Layout 属性必须使用单引号
```html
<!-- 正确 -->
<Layout title='{{TITLE}} - Lysander' description='{{DESC}}'>

<!-- 错误：双引号无法处理含引号的标题 -->
<Layout title="{{TITLE}} - Lysander" description="{{DESC}}">
```

### 2. 占位符
- `{{TITLE}}` - 文章标题（会自动 HTML 转义引号）
- `{{DESC}}` - 文章描述
- `{{DATE}}` - 发布日期
- `{{TAG_HTML}}` - 标签 HTML 片段

### 3. 代码块大括号
发布脚本会自动将 `<code>` 块内的 `{` `}` 转为 `&#123;` `&#125;`，避免 esbuild 解析错误。

## 发布流程
```bash
bash /home/ubuntu/scripts/harness-daily-publish.sh
```
自动执行：扫描当天文章 → 解析 METADATA → 替换占位符 → 更新索引 → 构建网站 → 发送微信草稿箱

## 历史修复记录
- 2026-04-07: 修复 JSON 中文引号、单引号属性、代码块大括号转义问题
- 2026-04-07: 修复微信公众号工作流 - 动态 digest 生成、多文章循环、定时触发器自动获取当天文章
- 2026-04-07: 定时触发器时区修复 - cron 从 `45 22` (UTC) 改为 `45 18` (UTC) = 迪拜 22:45
- 2026-04-09: **严禁直接cp文件到website目录，必须用harness-daily-publish.sh**

## 打包文件
- SOP 文档: `/home/ubuntu/knowledge-base/📝-Articles/harness-daily-publish-sop.md`
- 安装脚本: `/home/ubuntu/scripts/harness-install.sh`
- 工作流导出: `/home/ubuntu/knowledge-base/📝-Articles/harness-wechat-workflow-export.json`

## 微信公众号发布工作流

**Workflow ID**: `LGkeWFUdYx5X7vgP`
**Webhook URL**: `https://n8n.lysander.bond/webhook/wechat-blog-draft`
**定时触发**: 每天 22:45 (Dubai 时间)

### 核心功能
1. **Webhook 触发** - 接收 `article_url` 参数，发布指定文章
2. **定时触发** - 自动获取当天文章列表并发布
3. **多文章支持** - 使用 Split in Batches 循环处理多篇文章
4. **动态 Digest** - 从文章内容前 54 字符自动生成

### 调用方式
```bash
# 发布单篇文章
curl -X POST https://n8n.lysander.bond/webhook/wechat-blog-draft \
  -H "Content-Type: application/json" \
  -d '{"article_url": "https://lysander.bond/blog/claudecode"}'
```

### 工作流结构
```
Webhook/Daily Trigger
    ↓
Fetch Blog Index (定时触发时获取文章列表)
    ↓
Extract Today Articles (解析当天文章)
    ↓
Split in Batches (循环多篇文章)
    ↓
Fetch Article → Get Token → Convert HTML & Build Request → Create Draft
```
