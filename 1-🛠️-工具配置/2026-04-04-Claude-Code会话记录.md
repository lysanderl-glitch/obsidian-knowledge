# Claude Code 配置会话记录 (2026-04-04)

## 主题一：Claude Code 版本升级

### 操作
- 升级 Claude Code：2.1.89 → 2.1.91
- 命令：`sudo npm update -g @anthropic-ai/claude-code`

### 验证
```bash
claude --version  # 2.1.91 (Claude Code)
```

---

## 主题二：cc-connect 微信接入配置

### 安装
```bash
# 安装 beta 版本（支持微信个人号）
sudo npm install -g cc-connect@beta
```

### 微信扫码绑定
```bash
cc-connect weixin setup --project my-project
```
- 通过 ilink 机器人扫码连接
- Token：`84be8d67aaa4@im.bot:0600004ba3c8182d6450d0511dbe69f1d49b75`

### 配置修改
文件：`~/.cc-connect/config.toml`

修改 `work_dir` 为实际路径：
```toml
[projects.agent.options]
work_dir = "/home/ubuntu"
```

### 守护进程
```bash
cc-connect daemon install --config ~/.cc-connect/config.toml
cc-connect daemon start
cc-connect daemon restart  # 重启
cc-connect daemon status    # 查看状态
```

### 日志位置
`/home/ubuntu/.cc-connect/logs/cc-connect.log`

### 常见问题
- 二维码过期：需在服务器终端直接操作，非远程 SSH
- 启动失败：检查 `work_dir` 是否存在

---

## 主题三：Notion MCP Server 配置

### 安装
```bash
sudo npm install -g @notionhq/notion-mcp-server
```

### MCP 配置
文件：`~/.claude.json`

```json
"notion": {
  "type": "stdio",
  "command": "node",
  "args": [
    "/usr/lib/node_modules/@notionhq/notion-mcp-server/bin/cli.mjs"
  ],
  "env": {
    "NOTION_API_KEY": "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"
  }
}
```

### 验证
```bash
claude mcp list
# notion: node /usr/lib/node_modules/@notionhq/notion-mcp-server/bin/cli.mjs - ✓ Connected
```

### API Key
- 类型：Internal Integration Token
- 获取：https://www.notion.so/my-integrations

### 工作区数据库
| 数据库 | ID |
|--------|-----|
| 📋 测试用例数据库 | a4aafded-df30-4d18-aeae-1f6ce282c6a4 |
| ❓ FAQ 问答数据库 | f4466ba6-167f-4d79-a529-05ae555e8d75 |
| GTD Tasks | dc993148-7287-422b-b23c-630d92784d42 |
| 📁 Cowork 成果 | cf46964d-3c8a-4542-8b89-c56c422150be |
| 👤 团队成员目录 | b3e492f6-771f-4a5d-84a3-48a93da12bab |
| 📁 项目库 | 85159686-3f00-48b3-9d60-63f056ce1ee5 |
| 👥 干系人登记册 | b6fea694-4e43-4632-aeb5-dfbe49163ccb |

---

## 主题四：Obsidian 知识库状态

### 路径
`/home/ubuntu/knowledge-base/`

### 同步方式
- Git 同步（已完成配置）
- GitHub 仓库：https://github.com/lysanderl-glitch/obsidian-knowledge
- 自动同步：Cron 任务每小时 :30 分执行

### 相关文件
- 同步脚本：`/home/ubuntu/knowledge-base/sync.sh`
- 同步日志：`/home/ubuntu/knowledge-base/sync.log`

---

## 待办事项

- [ ] FAQ 问答数据库内容填充
- [ ] 测试 cc-connect 微信对话功能
- [ ] 验证 Notion MCP 读写操作

---

## 相关链接

- [cc-connect GitHub](https://github.com/chenhg5/cc-connect)
- [Notion API 文档](https://developers.notion.com/)
- [Claude Code 文档](https://docs.anthropic.com/claude-code)
