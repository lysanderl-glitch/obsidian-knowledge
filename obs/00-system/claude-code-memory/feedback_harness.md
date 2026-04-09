---
name: feedback_harness_self_healing
description: Harness Self-Healing 项目关键调试经验和解决方案
type: feedback
---

## MiniMax API 响应格式问题
**问题:** Claude API 调用返回空 analysis_text
**原因:** MiniMax-M2 模型返回的 content 数组第一个元素是 `type: "thinking"` 而非 `type: "text"`
**解决方案:** 在 API payload 中添加 `"thinking": {"type": "disabled"}`

```python
api_payload = {
    "model": "claude-3-5-sonnet-20241022",
    "max_tokens": 500,
    "thinking": {"type": "disabled"},  # 关键修复
    "messages": [{"role": "user", "content": prompt}]
}
```

## 端口占用问题
**问题:** OSError: [Errno 98] Address already in use
**解决方案:** 使用 `socketserver.TCPServer.allow_reuse_address = True` 或换端口

## max_tokens 不足
**问题:** JSON 响应被截断，confidence/category 返回 null
**解决方案:** max_tokens 从 300 增加到 500

## Docker 网络访问
**n8n 容器 → 主机:** 使用 `172.18.0.1` 而非 `127.0.0.1`

**Why:** 这些问题都是首次部署 self-healing 系统时的典型调试场景，下次遇到可快速定位
**How to apply:** 调试 harness-analyze-server.py 时优先检查：thinking 禁用、端口占用、token 数量

## 博客发布系统典型错误（2026-04-07）

### 问题：昨天调试成果没有固化

**现象：** 昨天已经修复并成功运行的博客发布系统，今天重新运行时出现同样错误

**根本原因：**
1. 知识没有积累到可复用的记忆系统中
2. 系统规范没有固化为标准操作流程
3. 调试经验只在对话中，没有写入持久化记忆

**典型错误清单：**

| 错误 | 原因 | 解决方案 |
|------|------|---------|
| JSON 解析失败 | METADATA 中中文引号 `"` `"` 未转义 | 标题内引号必须写成 `\"` |
| HTML 属性解析错误 | 模板使用双引号包裹含引号的标题 | Layout 属性必须用单引号 `title='{{TITLE}}'` |
| esbuild 解析错误 | `<code>` 块内的 `{` `}` 被当作 JS 对象 | 代码块内大括号转义为 `&#123;` `&#125;` |
| 微信公众号 digest 硬编码 | 每次都要手动改 | 从文章内容前 54 字符动态生成 |

**Harness Engineering 核心教训：**
> 每次犯错误，都要花时间设计解决方案确保永远不再犯。如果同一个错误出现两次，说明第一次的修复只是临时补丁，没有真正固化到系统中。

**固化清单（每次修复后执行）：**
1. 是否更新了记忆文件？
2. 是否固化了模板/脚本？
3. 是否记录了错误模式和解决方案？
4. 其他人会遇到同样的问题吗？

**Why:** 博客系统和微信公众号工作流的调试经验，下次创建文章或修改工作流时可快速参考
**How to apply:** 遇到博客发布问题先查 memory/blog_article_system.md；遇到微信工作流问题查同一文件的微信部分

## GitHub Secret Scanning 推送失败（2026-04-07）

### 问题
GitHub 推送被拒绝，报错 `GH013: Repository rule violations - Push cannot contain secrets`

### 原因
1. Session 文件（会话记录）包含 Slack Webhook URL
2. Session 文件被提交到 Git 仓库
3. GitHub secret scanning 检测到并阻止推送

### 解决方案
1. **立即修复**：将 session 文件从 Git 跟踪中移除
   ```bash
   echo "3-💡-经验总结/sessions/" >> .gitignore
   git rm -r --cached "3-💡-经验总结/sessions/"
   ```

2. **彻底解决**：避免将 session 文件提交到 Git
   - Session 文件保留在本地即可
   - 后续同步时先添加到 .gitignore

3. **Pre-push hook**：添加检查防止未来再犯
   ```bash
   # 检查将要推送的文件，阻止包含真实 webhook URL
   grep -r "hooks.slack.com/services/[A-Za-z0-9/]*" "$@" 2>/dev/null | grep -v REDACTED
   ```

### 预防措施
- Session 文件（`3-💡-经验总结/sessions/`）已加入 .gitignore
- 真实 webhook URL 已替换为 REDACTED
- Pre-push hook 已安装并修复

**Why:** 避免因 secret scanning 阻塞 CI/CD 或团队协作
**How to apply:** 任何包含 webhook/API key 的文件先加 .gitignore 再 commit

## n8n Workflow JavaScript 代码错误（2026-04-08）

### 问题
`missing ) after argument list` - JavaScript 语法错误导致工作流执行失败

### 根本原因
1. **单引号嵌套问题**：代码块中 `Courier New` 包含单引号，破坏 JavaScript 字符串
   ```javascript
   // 错误写法
   clean.replace(/<code[^>]*>/gi, '<code style="font-family:Courier New,monospace...">');
   
   // 正确写法：使用 HTML 实体
   clean.replace(/<code[^>]*>/gi, '<code style="font-family:&quot;Courier New&quot;,monospace...">');
   ```

2. **n8n Code 节点 mode 参数**：`run_once_for_all_items` → `runOnceForAllItems`

3. **变量名不一致**：声明 `article_url` 但使用 `articleUrl`

4. **数据流问题**：需要 Merge 节点合并多个数据源

### 固化清单
- [x] 将错误模式写入记忆文件
- [x] 更新 wechat-article-best-practices.md 添加注意事项
- [ ] 在 n8n workflow SDK 使用规范中明确禁止在 JS 字符串中使用单引号字体名

**Why:** 这是第二次因同样原因（Javascript 单引号）导致工作流失败，必须彻底固化
**How to apply:** 在 n8n Code 节点中处理样式字符串时，字体名等包含单引号的内容必须使用 HTML 实体 `&quot;` 替代 `'`