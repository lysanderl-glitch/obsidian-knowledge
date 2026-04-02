# Obsidian + Git 同步配置

## 目标
将服务器 `/home/ubuntu/knowledge-base` 的知识库通过 Git 同步到 GitHub，实现多设备同步。

## 环境信息

### 服务器
- 用户：`ubuntu`
- 知识库路径：`/home/ubuntu/knowledge-base`
- GitHub 仓库：https://github.com/lysanderl-glitch/obsidian-knowledge

### GitHub
- 仓库名：`obsidian-knowledge`
- Token：`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`

## 配置步骤

### 1. 服务器端初始化

```bash
cd /home/ubuntu/knowledge-base

# 添加远程仓库
git remote add origin https://github.com/lysanderl-glitch/obsidian-knowledge.git

# 首次推送（token 已内嵌在 URL 中）
git remote set-url origin https://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@github.com/lysanderl-glitch/obsidian-knowledge.git
git push -u origin master
```

### 2. 自动同步脚本

文件位置：`/home/ubuntu/knowledge-base/sync.sh`

```bash
#!/bin/bash
cd /home/ubuntu/knowledge-base || exit 1
git pull origin master
git push origin master
```

### 3. Cron 任务

每小时 `:30` 分执行同步：
```bash
(crontab -l 2>/dev/null | grep -v "sync.sh"; echo "30 * * * * /home/ubuntu/knowledge-base/sync.sh >> /home/ubuntu/knowledge-base/sync.log 2>&1") | crontab -
```

查看 cron 任务：
```bash
crontab -l
```

### 4. Windows 端配置

1. 安装 [Obsidian](https://obsidian.md)
2. 安装 **Obsidian Git** 插件
3. 克隆仓库：
   ```bash
   git clone https://github.com/lysanderl-glitch/obsidian-knowledge.git
   ```
4. 用 Obsidian 打开克隆的文件夹作为 Vault
5. 配置插件自动同步间隔（推荐 5 分钟）

## 相关文件

- 同步脚本：`/home/ubuntu/knowledge-base/sync.sh`
- 同步日志：`/home/ubuntu/knowledge-base/sync.log`

## 注意事项

- 服务器端 git 认证已保存在 remote URL 中
- 如需更新 token，执行：
  ```bash
  git remote set-url origin https://NEW_TOKEN@github.com/lysanderl-glitch/obsidian-knowledge.git
  ```
