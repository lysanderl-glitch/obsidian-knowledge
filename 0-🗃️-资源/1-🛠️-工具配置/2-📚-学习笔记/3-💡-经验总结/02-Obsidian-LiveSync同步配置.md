---
created: 2026-04-02
updated: 2026-04-02
tags:
  - Obsidian
  - LiveSync
  - CouchDB
  - 同步
  - Nginx
  - SSL
---

# Obsidian LiveSync 同步配置

## 方案选择

使用 **LiveSync + CouchDB** 方案实现本地与服务器的 Obsidian 实时同步。

## 服务器配置

### 1. CouchDB Docker 部署

```yaml
# docker-compose.yml
version: '3'
services:
  couchdb:
    image: couchdb:3
    container_name: couchdb
    ports:
      - "5984:5984"
    environment:
      COUCHDB_USER: admin
      COUCHDB_PASSWORD: your_password
    volumes:
      - ./data:/opt/couchdb/data
    restart: unless-stopped
```

**关键端口：**
- `5984`: CouchDB HTTP API
- `9100`: Erlang分布式端口（集群用）

### 2. Nginx 反向代理配置

```nginx
# /etc/nginx/sites-available/couchdb
server {
    listen 443 ssl;
    server_name couchdb.lysander.bond;

    ssl_certificate /etc/letsencrypt/live/couchdb.lysander.bond/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/couchdb.lysander.bond/privkey.pem;

    location / {
        proxy_pass http://localhost:5984;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # CouchDB 特定头
        proxy_buffering off;
        proxy_read_timeout 90;
    }
}

server {
    listen 80;
    server_name couchdb.lysander.bond;
    return 301 https://$server_name$request_uri;
}
```

### 3. Let's Encrypt SSL 证书

```bash
# 安装 certbot
sudo apt install certbot python3-certbot-nginx

# 获取证书
sudo certbot --nginx -d couchdb.lysander.bond
```

### 4. CouchDB 管理员账户

首次安装后需要创建管理员：
1. 访问 https://couchdb.lysander.bond/_utils/
2. 创建管理员账户（用户名/密码）
3. 启用 `chaos` 视图（可选）

---

## 本地 Obsidian 配置

### 安装 LiveSync 插件

1. 打开 Obsidian 设置
2. 进入 **社区插件** → **浏览**
3. 搜索 "LiveSync"
4. 安装并启用

### 配置连接

1. 进入 LiveSync 插件设置
2. **Server URL**: `https://couchdb.lysander.bond`
3. **Username**: 你的CouchDB用户名
4. **Password**: 你的CouchDB密码
5. **Database Name**: `obsidian`（或自定义）

### 同步选项

- ✅ `LiveSync`: 启用实时同步
- ✅ `Use so-called LiveSync`: 实时同步模式
- `Number of saved revisions`: 10（可选）

### 初始化同步

1. 点击 **"Initialize new database"**（首次）
2. 或 **"Add existing database"**（已有数据）
3. 等待同步完成

---

## 验证同步

### CouchDB 管理界面
- 访问: https://couchdb.lysander.bond/_utils/
- 查看数据库列表和文档

### 测试创建
1. 在本地 Obsidian 创建测试笔记
2. 确认笔记出现在 CouchDB 中
3. 在另一设备上确认同步

---

## 替代方案

| 方案 | 特点 |
|------|------|
| **Remotely Save** | 支持S3/OneDrive/Dropbox |
| **Obsidian Git** | 使用Git版本控制 |
| **Syncthing** | P2P文件同步 |

---

## 常见问题排查

### 问题1: 连接被拒绝
- 检查CouchDB是否运行: `docker ps | grep couchdb`
- 检查端口: `curl http://localhost:5984`
- 检查防火墙: `sudo ufw status`

### 问题2: SSL证书错误
- 确认Nginx配置正确: `sudo nginx -t`
- 重新获取证书: `sudo certbot --reinstall -d couchdb.lysander.bond`

### 问题3: 同步缓慢
- 检查网络延迟
- 减少同步频率
- 清理CouchDB历史版本

---

## 相关资源

- [[Obsidian-知识库设计]]
- [[n8n工作流开发经验]]
