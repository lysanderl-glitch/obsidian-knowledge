---
name: project_stock_trading_active
description: 股票交易系统 - 活跃开发会话
type: project
---

# 股票交易系统 - 活跃工作状态 (2026-04-08)

## 当前会话信息
- **会话ID**: b89d1d50-a819-4a56-ba31-3b91f2ee4264
- **Slug**: eager-soaring-flask
- **最后更新**: 2026-04-08

## 项目概述
股票交易系统（趋势智选）- A股趋势交易指导系统

## 多Agent协作体系
- **管理Agent (Manager)** - 主会话，统筹协调
- **需求专家** - 需求分析
- **产品经理** - 功能设计
- **研发经理** - 技术实现
- **测试经理** - 质量验证
- **交易员Agent** - 交易执行、历史回测、每日复盘

## 系统架构
```
/home/ubuntu/stock-trading/
├── backend/ (FastAPI + SQLAlchemy)
│   ├── app/
│   │   ├── main.py
│   │   ├── models.py (数据库模型)
│   │   ├── routers/ (API路由)
│   │   │   ├── backtest.py (回测)
│   │   │   ├── simulation.py (模拟交易)
│   │   │   ├── auto_trading.py (自动化炒股)
│   │   │   ├── knowledge.py (知识库)
│   │   │   ├── position.py (仓位策略)
│   │   │   ├── stop_loss.py (止损监控) [新增V1]
│   │   │   ├── trend_filter.py (趋势过滤) [新增V2]
│   │   │   └── build_up.py (分批建仓) [新增V3]
│   │   └── backtest_engine.py
│   └── data/stock_trading.db
└── frontend/ (Vue3 + Pinia + Element Plus)
```

## 已完成功能模块

### V1.0 历史功能
1. **历史回测 (/backtest)** - 信号→次日开盘买入→止损/回测结束卖出
2. **模拟炒股 (/simulation)** - 初始资金100万，支持买卖交易
3. **仓位策略 (/position)** - 参照《炒股的智慧》分阶段建仓策略
4. **知识库 (/knowledge)** - 已录入《炒股的智慧》18条核心知识
5. **自动化炒股 (/auto-trading)** - 每日自动选择最佳突破/回踩信号

### V2.0 增强功能 (2026-04-08实施)

#### Phase 1: 止损监控
- API: `/api/v1/positions/{symbol}/stop-loss`
- 功能: 设置/获取/触发止损，止损预警记录
- 数据表: position_stop_loss, stop_loss_alert

#### Phase 2: 趋势过滤
- API: `/api/v1/trends/{symbol}`
- 功能: MA20/MA60趋势判断，上升/下降/横盘识别
- 数据表: trend_data

#### Phase 3: 分批建仓
- API: `/api/v1/build-ups/plans`
- 功能: 试仓20%→下跌10%加仓→满仓40%
- 数据表: build_up_plans, build_up_records

### V3.0 优化功能 (2026-04-08实施)

#### P0-1: 趋势确认指标
- 新增字段: ma_angle, rsi, atr, trend_days
- 过滤条件: MA角度>5度, RSI<65, 趋势天数>=3

#### P0-2: 动态止损
- 震荡市: entry - (2 × ATR)
- 趋势市: entry - (3 × ATR)

## 交易员Agent

### 回测结果 (2026-04-08)
| 回测周期 | 收益率 | 交易次数 | 胜率 |
|---------|-------|---------|------|
| 1个月 | -0.07% | 5 | 0.0% |
| 3个月 | -2.00% | 9 | 33.3% |
| 半年 | -4.99% | 14 | 14.3% |

### 策略问题诊断
1. MA20>MA60条件过严 → 调整为多周期确认
2. 7%固定止损在震荡市失效 → 动态止损(ATR)
3. 假突破多 → 增加趋势强度验证
4. 未考虑大盘环境 → 市场环境过滤器

### 每日复盘流程
1. 账户盈亏回顾
2. 交易信号有效性分析
3. 策略执行情况总结
4. 系统改进建议
5. 明日交易计划

## 生产环境
- **域名**: http://stock.lysander.bond/
- **后端**: http://localhost:8000
- **前端**: http://localhost:5173

## 数据库
- 路径: `/home/ubuntu/stock-trading/data/stock_trading.db`
- 表: stocks, daily_data, trading_signals(19字段含新增4个), simulation_account, simulation_positions, simulation_trades, backtest_records, backtest_trades, position_strategies, book_knowledge, position_stop_loss, stop_loss_alert, trend_data, build_up_plans, build_up_records

## 待验证/待优化
1. P0功能（趋势指标+动态止损）需实际交易验证
2. 回测数据不足（仅120天）
3. 策略胜率偏低需持续优化
4. 交易员每日复盘机制

## 相关文件
- 工作记录: `/home/ubuntu/knowledge-base/workRecord/stock-trading-2026-04-07/功能扩展.md`
- 日记素材: `/home/ubuntu/daily-record/inbox/`
- 交易员Agent: `/home/ubuntu/agents/trader_agent.py`
- 数据库迁移: `/home/ubuntu/stock-trading/backend/scripts/migrate_add_signal_columns.py`
