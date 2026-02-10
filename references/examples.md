# XtQuant 实战示例模板

## Table of Contents

- [1. 历史数据下载 + 读取](#1-历史数据下载--读取)
- [2. 实时订阅 + 回调处理](#2-实时订阅--回调处理)
- [3. 基础交易流程（连接/订阅/下单）](#3-基础交易流程连接订阅下单)
- [4. 对手价下单模板](#4-对手价下单模板)
- [5. 批量股票数据处理](#5-批量股票数据处理)
- [6. 复权数据读取模板](#6-复权数据读取模板)

## 1. 历史数据下载 + 读取

```python
from xtquant import xtdata

stocks = ["000001.SZ", "600000.SH"]

# 建议先补齐历史数据
for stock in stocks:
    xtdata.download_history_data(stock, period="1d", incrementally=True)

# 读取最近20根日线
data = xtdata.get_market_data_ex(
    field_list=["open", "high", "low", "close", "volume"],
    stock_list=stocks,
    period="1d",
    count=20,
)

print(data["close"])
```

## 2. 实时订阅 + 回调处理

```python
from xtquant import xtdata


def on_quote(datas):
    for stock, rows in datas.items():
        row = rows[0]
        print(stock, "lastPrice=", row.get("lastPrice"), "time=", row.get("time"))

seq = xtdata.subscribe_quote(
    stock_code="000001.SZ",
    period="tick",
    count=0,
    callback=on_quote,
)

print("subscribe seq:", seq)
xtdata.run()  # 回调模式要阻塞
```

## 3. 基础交易流程（连接/订阅/下单）

```python
import time
from xtquant.xttrader import XtQuantTrader, XtQuantTraderCallback
from xtquant.xttype import StockAccount
from xtquant import xtconstant


class TraderCallback(XtQuantTraderCallback):
    def on_stock_order(self, order):
        print("委托回报", order.order_id, order.stock_code, order.order_status)

    def on_stock_trade(self, trade):
        print("成交回报", trade.order_id, trade.stock_code, trade.traded_price, trade.traded_volume)

    def on_order_error(self, order_error):
        print("下单失败", order_error.order_id, order_error.error_id, order_error.error_msg)


path = r"D:\\迅投极速交易终端 睿智融科版\\userdata_mini"
session_id = int(time.time())
account = StockAccount("YOUR_ACCOUNT", "STOCK")

trader = XtQuantTrader(path, session_id)
trader.register_callback(TraderCallback())
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
    "demo_strategy",
    "demo",
)
print("order_id", order_id)
```

## 4. 对手价下单模板

```python
from xtquant import xtdata
from xtquant import xtconstant

stock = "000001.SZ"
tick = xtdata.get_full_tick([stock])[stock]

# 买入用卖一，卖出用买一
buy_price = tick["askPrice"][0] if tick["askPrice"][0] > 0 else tick["lastPrice"]
sell_price = tick["bidPrice"][0] if tick["bidPrice"][0] > 0 else tick["lastPrice"]

print("buy_price", buy_price)
print("sell_price", sell_price)

# 示例调用
# trader.order_stock(account, stock, xtconstant.STOCK_BUY, 100, xtconstant.FIX_PRICE, buy_price, "strategy", "buy")
# trader.order_stock(account, stock, xtconstant.STOCK_SELL, 100, xtconstant.FIX_PRICE, sell_price, "strategy", "sell")
```

## 5. 批量股票数据处理

```python
from xtquant import xtdata

stocks = ["000001.SZ", "600000.SH", "000002.SZ"]

for stock in stocks:
    xtdata.download_history_data(stock, period="1d", incrementally=True)

data = xtdata.get_market_data_ex(
    field_list=["close", "volume"],
    stock_list=stocks,
    period="1d",
    count=30,
)

close_df = data["close"]
for stock in stocks:
    prices = close_df[stock].dropna()
    if len(prices) >= 2:
        pct = (prices.iloc[-1] / prices.iloc[-2] - 1) * 100
        print(stock, f"{pct:.2f}%")
```

## 6. 复权数据读取模板

```python
from xtquant import xtdata

stock = "000001.SZ"

none_data = xtdata.get_market_data_ex(["close"], [stock], period="1d", dividend_type="none")
front_data = xtdata.get_market_data_ex(["close"], [stock], period="1d", dividend_type="front")
back_data = xtdata.get_market_data_ex(["close"], [stock], period="1d", dividend_type="back")

factors = xtdata.get_divid_factors(stock)

print("none:")
print(none_data["close"].tail())
print("front:")
print(front_data["close"].tail())
print("back:")
print(back_data["close"].tail())
print("factors:")
print(factors.tail())
```

---

## 使用建议

- 示例优先在模拟账户验证，再切换实盘。
- 交易回调中不要执行耗时任务，避免阻塞推送线程。
- 实盘前增加风控（可用资金校验、涨跌停校验、下单频控、重试策略）。
