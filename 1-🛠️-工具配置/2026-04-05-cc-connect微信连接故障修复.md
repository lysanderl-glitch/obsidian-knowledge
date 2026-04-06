# cc-connect 微信连接故障修复记录

## 问题现象

微信端收到 "not logged in please run /login" 错误，cc-connect 日志显示：
- `input_tokens=0 output_tokens=0` — Claude Code 没有发起 API 调用
- 每次消息返回固定的 34 字符响应

## 根本原因

**cc-connect 的 systemd user service 没有继承 ANTHROPIC_API_KEY 等环境变量。**

Claude Code 在 tmux 交互式 shell 中运行时已登录（API Key 已配置），但 cc-connect 作为 systemd 服务独立运行，其子进程无法访问这些环境变量。

## 诊断过程

1. 检查 cc-connect 日志：`tail ~/.cc-connect/logs/cc-connect.log`
2. 检查进程：`ps aux | grep claude` — 发现 cc-connect 启动了额外的 Claude Code 子进程
3. 检查 systemd 环境：`systemctl --user show-environment` — 确认缺少 ANTHROPIC 相关变量
4. 测试 Claude Code：`claude -p "say hello"` — 交互式 shell 中 API 正常

## 解决方案

将环境变量添加到 systemd user 环境：

```bash
systemctl --user set-environment "ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY"
systemctl --user set-environment "ANTHROPIC_BASE_URL=$ANTHROPIC_BASE_URL"
systemctl --user set-environment "ANTHROPIC_MODEL=$ANTHROPIC_MODEL"
```

验证：
```bash
systemctl --user show-environment | grep ANTHROPIC
```

然后重启 cc-connect：
```bash
cc-connect daemon restart
```

## 关键经验

- systemd 服务默认不继承用户 shell 的环境变量
- 进程通信类服务（如 cc-connect）需要显式配置环境变量
- API Key 等认证信息不能依赖隐式继承

## 补充说明

### 环境变量持久化

`systemctl --user set-environment` 设置的环境变量**在服务器重启后会丢失**。

如需永久生效，创建 systemd 环境文件：
```bash
mkdir -p ~/.config/environment.d/
echo "ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY" >> ~/.config/environment.d/cc-connect.conf
```

**本次不处理**。如再次遇到相同问题，先用 `systemctl --user show-environment | grep ANTHROPIC` 检查变量是否丢失，如丢失则重新设置即可快速恢复。
