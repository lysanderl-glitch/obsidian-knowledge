---
created: 2026-04-02
tags:
  - Claude-Code
  - 环境配置
  - 故障排除
---

# Claude Code 环境变量问题

## 问题描述

Claude Code 打开后需要手动加载环境变量，无法自动加载。

## 解决方案

在 `~/.bashrc` 中添加环境变量加载逻辑。

**注意：** 不要在 `.bashrc` 中 source `.profile`，会导致循环引用。

## 相关知识

### .bashrc 和 .profile 循环引用问题

**原因：** `.bashrc` 最后一行 `[[ -f ~/.profile ]] && . ~/.profile` 会与 `.profile` 中 `if [ -f ".bashrc" ]` 相互引用，导致登录失败。

**解决方法：** 只在 `.profile` 中加载环境变量，不要在 `.bashrc` 中 source `.profile`。

## 参考

- [[.bashrc-profile循环引用问题]]
