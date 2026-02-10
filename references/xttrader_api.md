# xttrader API 详细参考

## Table of Contents

- [1. 初始化与连接](#1-初始化与连接)
- [2. 账号与常量](#2-账号与常量)
- [3. 交易接口](#3-交易接口)
- [4. 查询接口](#4-查询接口)
- [5. 信用接口](#5-信用接口)
- [6. 回调机制](#6-回调机制)
- [7. 典型流程](#7-典型流程)

## 1. 初始化与连接

### 创建交易实例

```python
XtQuantTrader(path, session_id)
```

- `path`：MiniQMT 数据目录（通常 `userdata_mini`）
- `session_id`：会话编号，不同策略进程建议不同

### 生命周期方法

```python
register_callback(callback)
start()
connect()        # 0 成功，非0失败
stop()
run_forever()    # 阻塞线程，接收推送
```

### 订阅账号推送

```python
subscribe(account)   # 0 成功, -1 失败
unsubscribe(account)
```

## 2. 账号与常量

### 账号类型

- `STOCK`
- `CREDIT`
- `FUTURE`
- `STOCK_OPTION`
- `FUTURE_OPTION`
- `HUGANGTONG`
- `SHENGANGTONG`

### 常见委托方向（示例）

- 股票：`STOCK_BUY` `STOCK_SELL`
- 信用：`CREDIT_BUY` `CREDIT_SELL` `CREDIT_FIN_BUY` `CREDIT_SLO_SELL` ...
- 期货：`FUTURE_OPEN_LONG` `FUTURE_OPEN_SHORT` `FUTURE_CLOSE_*`

### 常见报价类型（示例）

- `LATEST_PRICE`
- `FIX_PRICE`
- `MARKET_BEST`
- `MARKET_CANCEL`
- `MARKET_CANCEL_1`
- `MARKET_CANCEL_5`

> 注：部分市价类型只在实盘环境生效。

## 3. 交易接口

### 同步下单

```python
order_stock(account, stock_code, order_type, order_volume, price_type, price, strategy_name='', order_remark='')
```

- 返回 `order_id`，失败返回 `-1`
- `order_remark` 在 MiniQMT 客户端有长度限制

### 异步下单

```python
order_stock_async(account, stock_code, order_type, order_volume, price_type, price, strategy_name='', order_remark='')
```

- 返回请求序号 `seq`
- 结果通过回调（如 `on_order_stock_async_response`）追踪

### 同步撤单

```python
cancel_order_stock(account, order_id)
```

- 返回 `0` 成功，`-1` 失败

### 异步撤单

```python
cancel_order_stock_async(account, order_id)
```

- 返回请求序号 `seq`

### 按柜台合同号撤单

```python
cancel_order_stock_sysid(account, market, order_sysid)
cancel_order_stock_sysid_async(account, market, order_sysid)
```

- `market`：`0` 上海，`1` 深圳

### 资金划拨

```python
fund_transfer(account, transfer_direction, price)
```

## 4. 查询接口

### 资产、委托、成交、持仓

```python
query_stock_asset(account)
query_stock_orders(account, cancelable_only=False)
query_stock_trades(account)
query_stock_positions(account)
query_stock_position(account, stock_code)
```

### 其他查询

```python
query_new_purchase_limit(account)
query_ipo_data()
query_account_infos()
query_account_status()
```

## 5. 信用接口

```python
query_credit_detail(account)
query_stk_compacts(account)
query_credit_subjects(account)
query_credit_slo_code(account)
query_credit_assure(account)
```

## 6. 回调机制

继承 `XtQuantTraderCallback` 并重写需要的方法：

```python
class MyCallback(XtQuantTraderCallback):
    def on_disconnected(self):
        pass

    def on_stock_order(self, order):
        pass

    def on_stock_trade(self, trade):
        pass

    def on_order_error(self, order_error):
        pass

    def on_cancel_error(self, cancel_error):
        pass

    def on_order_stock_async_response(self, response):
        pass

    def on_account_status(self, status):
        pass
```

### 回调设计建议

- 回调只做轻量逻辑（日志、入队、状态更新）
- 不在回调里做耗时 IO / 大计算
- 异步下单需同时监听成功回报与错误回报

## 7. 典型流程

```python
import time
from xtquant.xttrader import XtQuantTrader, XtQuantTraderCallback
from xtquant.xttype import StockAccount
from xtquant import xtconstant

path = r"D:\\迅投极速交易终端 睿智融科版\\userdata_mini"
session_id = int(time.time())

class CB(XtQuantTraderCallback):
    def on_stock_trade(self, trade):
        print("成交", trade.stock_code, trade.traded_price, trade.traded_volume)

trader = XtQuantTrader(path, session_id)
account = StockAccount("YOUR_ACCOUNT", "STOCK")
trader.register_callback(CB())
trader.start()

if trader.connect() != 0:
    raise RuntimeError("connect failed")
if trader.subscribe(account) != 0:
    raise RuntimeError("subscribe failed")

order_id = trader.order_stock(
    account,
    "600000.SH",
    xtconstant.STOCK_BUY,
    100,
    xtconstant.FIX_PRICE,
    10.5,
    "demo",
    "demo_order",
)
print("order_id", order_id)
```
