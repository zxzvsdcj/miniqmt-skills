# XtQuant 数据结构与字段字典

## Table of Contents

- [1. 行情字段](#1-行情字段)
- [2. 财务字段](#2-财务字段)
- [3. 交易对象字段](#3-交易对象字段)
- [4. 常量速查](#4-常量速查)

## 1. 行情字段

### Tick 分笔字段（常见）

| 字段 | 类型 | 说明 |
|---|---|---|
| `time` | int | 毫秒时间戳 |
| `lastPrice` | float | 最新价 |
| `open` | float | 开盘价 |
| `high` | float | 最高价 |
| `low` | float | 最低价 |
| `lastClose` | float | 前收盘价 |
| `amount` | float | 成交额 |
| `volume` | float | 成交量 |
| `pvolume` | float | 原始成交量 |
| `stockStatus` | int | 证券状态 |
| `openInt` | float | 持仓量（期货） |
| `lastSettlementPrice` | float | 前结算价（期货） |
| `askPrice` | list | 卖价数组（多档） |
| `bidPrice` | list | 买价数组（多档） |
| `askVol` | list | 卖量数组（多档） |
| `bidVol` | list | 买量数组（多档） |
| `transactionNum` | int | 成交笔数 |

### K线字段（1m/5m/1d 等）

| 字段 | 类型 | 说明 |
|---|---|---|
| `time` | int | 毫秒时间戳 |
| `open` | float | 开盘价 |
| `high` | float | 最高价 |
| `low` | float | 最低价 |
| `close` | float | 收盘价 |
| `volume` | float | 成交量 |
| `amount` | float | 成交额 |
| `settelementPrice` | float | 今结算价（期货） |
| `openInterest` | float | 持仓量（期货） |
| `preClose` | float | 前收盘价 |
| `suspendFlag` | int | 停牌标记（0正常/1停牌/-1复牌） |

### Level2 快照（l2quote）

| 字段 | 说明 |
|---|---|
| `askPrice` / `bidPrice` | 多档委卖/委买价 |
| `askVol` / `bidVol` | 多档委卖/委买量 |
| `pe` | 市盈率（示例字段） |

### Level2 逐笔委托（l2order）

| 字段 | 类型 | 说明 |
|---|---|---|
| `time` | int | 时间戳 |
| `price` | float | 委托价 |
| `volume` | int | 委托量 |
| `entrustNo` | str | 委托号 |
| `entrustType` | int | 委托类型 |
| `entrustDirection` | int | 委托方向 |

`entrustDirection` 常见值：

- `1` 买入
- `2` 卖出
- `3` 撤买（上交所）
- `4` 撤卖（上交所）

### Level2 逐笔成交（l2transaction）

| 字段 | 类型 | 说明 |
|---|---|---|
| `time` | int | 时间戳 |
| `price` | float | 成交价 |
| `volume` | int | 成交量 |
| `amount` | float | 成交额 |
| `tradeIndex` | int | 成交记录号 |
| `buyNo` | int | 买方委托号 |
| `sellNo` | int | 卖方委托号 |
| `tradeType` | int | 成交类型 |
| `tradeFlag` | int | 成交标志 |

`tradeFlag` 常见值：

- `0` 未知
- `1` 外盘（主动买）
- `2` 内盘（主动卖）
- `3` 撤单（深交所）

### 证券状态（`stockStatus`）

| 值 | 状态 | 说明 |
|---|---|---|
| `11` | S | 开盘前 |
| `12` | C | 集合竞价 |
| `13` | T | 连续交易 |
| `14` | B | 休市 |
| `15` | E | 闭市 |
| `17` | P | 临时停牌 |
| `18` | U | 收盘集合竞价 |
| `21` | - | 字段异常 |

## 2. 财务字段

### 财务表类型

- `Balance`：资产负债表
- `Income`：利润表
- `CashFlow`：现金流量表
- `Capital`：股本表
- `Holdernum`：股东数
- `Top10holder`：十大股东
- `Top10flowholder`：十大流通股东
- `Pershareindex`：每股指标

### Balance（示例字段）

- `tot_assets`：资产总计
- `tot_liab`：负债合计
- `total_equity`：所有者权益合计
- `cash_equivalents`：货币资金
- `account_receivable`：应收账款
- `inventories`：存货
- `fixed_capital`：固定资产

### Income（示例字段）

- `revenue_inc`：营业收入
- `total_expense`：营业成本
- `oper_profit`：营业利润
- `tot_profit`：利润总额
- `net_profit_incl_min_int_inc`：净利润
- `s_fa_eps_basic`：基本每股收益
- `s_fa_eps_diluted`：稀释每股收益

### CashFlow（示例字段）

- `goods_sale_and_service_render_cash`：销售商品/劳务收到现金
- `goods_and_services_cash_paid`：购买商品/劳务支付现金
- `net_cash_flows_oper_act`：经营活动现金流净额
- `net_cash_flows_inv_act`：投资活动现金流净额
- `net_cash_flows_fnc_act`：筹资活动现金流净额

### Capital（示例字段）

- `total_capital`：总股本
- `circulating_capital`：已上市流通股本
- `restrict_circulating_capital`：限售流通股份

### Pershareindex（示例字段）

- `s_fa_bps`：每股净资产
- `s_fa_eps_basic`：基本每股收益
- `s_fa_eps_diluted`：稀释每股收益
- `s_fa_ocfps`：每股经营现金流
- `du_return_on_equity`：净资产收益率
- `gear_ratio`：资产负债比率

## 3. 交易对象字段

### XtAsset

| 字段 | 类型 | 说明 |
|---|---|---|
| `account_type` | int | 账号类型 |
| `account_id` | str | 资金账号 |
| `cash` | float | 可用金额 |
| `frozen_cash` | float | 冻结金额 |
| `market_value` | float | 持仓市值 |
| `total_asset` | float | 总资产 |

### XtOrder

| 字段 | 类型 | 说明 |
|---|---|---|
| `account_id` | str | 资金账号 |
| `stock_code` | str | 证券代码 |
| `order_id` | int | 订单编号 |
| `order_sysid` | str | 柜台合同编号 |
| `order_time` | int | 报单时间 |
| `order_type` | int | 委托类型 |
| `order_volume` | int | 委托数量 |
| `price_type` | int | 报价类型 |
| `price` | float | 委托价格 |
| `traded_volume` | int | 已成交数量 |
| `traded_price` | float | 成交均价 |
| `order_status` | int | 委托状态 |
| `status_msg` | str | 状态描述 |
| `strategy_name` | str | 策略名 |
| `order_remark` | str | 备注 |
| `direction` | int | 多空方向（期货） |
| `offset_flag` | int | 开平标记 |

### XtTrade

| 字段 | 类型 | 说明 |
|---|---|---|
| `account_id` | str | 资金账号 |
| `stock_code` | str | 证券代码 |
| `order_type` | int | 委托类型 |
| `traded_id` | str | 成交编号 |
| `traded_time` | int | 成交时间 |
| `traded_price` | float | 成交均价 |
| `traded_volume` | int | 成交数量 |
| `traded_amount` | float | 成交金额 |
| `order_id` | int | 订单编号 |
| `order_sysid` | str | 柜台合同编号 |
| `strategy_name` | str | 策略名 |
| `order_remark` | str | 备注 |
| `direction` | int | 多空方向 |
| `offset_flag` | int | 开平标记 |

### XtPosition

| 字段 | 类型 | 说明 |
|---|---|---|
| `account_id` | str | 资金账号 |
| `stock_code` | str | 证券代码 |
| `volume` | int | 持仓数量 |
| `can_use_volume` | int | 可用数量 |
| `open_price` | float | 开仓价 |
| `market_value` | float | 持仓市值 |
| `frozen_volume` | int | 冻结数量 |
| `on_road_volume` | int | 在途股份 |
| `avg_price` | float | 成本价 |
| `direction` | int | 多空方向 |

## 4. 常量速查

### 账号类型

- `STOCK` `CREDIT` `FUTURE` `STOCK_OPTION` `FUTURE_OPTION` `HUGANGTONG` `SHENGANGTONG`

### 股票委托类型

- `STOCK_BUY` `STOCK_SELL`

### 期货委托类型（示例）

- `FUTURE_OPEN_LONG` `FUTURE_OPEN_SHORT`
- `FUTURE_CLOSE_LONG_HISTORY` `FUTURE_CLOSE_LONG_TODAY`
- `FUTURE_CLOSE_SHORT_HISTORY` `FUTURE_CLOSE_SHORT_TODAY`

### 报价类型（示例）

- `LATEST_PRICE`
- `FIX_PRICE`
- `MARKET_BEST`
- `MARKET_CANCEL`
- `MARKET_CANCEL_1`
- `MARKET_CANCEL_5`

### 委托状态

| 常量 | 值 | 说明 |
|---|---|---|
| `ORDER_UNREPORTED` | 48 | 未报 |
| `ORDER_WAIT_REPORTING` | 49 | 待报 |
| `ORDER_REPORTED` | 50 | 已报 |
| `ORDER_REPORTED_CANCEL` | 51 | 已报待撤 |
| `ORDER_PARTSUCC_CANCEL` | 52 | 部成待撤 |
| `ORDER_PART_CANCEL` | 53 | 部撤 |
| `ORDER_CANCELED` | 54 | 已撤 |
| `ORDER_PART_SUCC` | 55 | 部成 |
| `ORDER_SUCCEEDED` | 56 | 已成 |
| `ORDER_JUNK` | 57 | 废单 |
