---
title: Harness 错误库
description: Claude Code 错误分析记录与解决方案
---

# Harness 错误库

存储 Claude Code 执行过程中遇到的错误及其解决方案。

## 目的

当错误发生时：
1. 检查知识库是否有相似错误
2. 若命中 → 返回历史方案
3. 若未命中 → AI 分析 → 存入知识库

## 结构

```
4-🛡️-Harness错误库/
├── README.md           ← 本文件
├── _index.md           ← 索引
└── errors/            ← 错误记录
    └── YYYY-MM.md     ← 按月存储
```

## 记录格式

```markdown
---
type: harness-error
date: 2026-04-06T19:00:00+04:00
category: dependency
error_keywords: ["npm ERR", "ENOENT"]
---

## 错误
错误信息

## 分析
AI 分析结果

## 解决方案
修复命令/代码

## 预防
预防建议
```
