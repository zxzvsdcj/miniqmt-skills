# XtQuant API Index (完整接口索引)

> 目的：快速定位接口名与签名。
> 细节参数、返回值、字段说明请继续查看 `xtdata_api.md` / `xttrader_api.md` / `data_structures.md`。

## Table of Contents

- [xtdata 行情模块](#xtdata-行情模块)
- [xttrader 交易模块](#xttrader-交易模块)
- [xtdatacenter（VIP连接扩展）](#xtdatacentervip连接扩展)

## xtdata 行情模块

### 订阅类

```python
subscribe_quote(stock_code, period='1d', start_time='', end_time='', count=0, callback=None)
subscribe_whole_quote(code_list, callback=None)
unsubscribe_quote(seq)
run()
```

### 行情读取类

```python
get_market_data_ex(field_list=[], stock_list=[], period='1d', start_time='', end_time='', count=-1, dividend_type='none', fill_data=True)
get_market_data(field_list=[], stock_list=[], period='1d', start_time='', end_time='', count=-1, dividend_type='none', fill_data=True)
get_local_data(field_list=[], stock_list=[], period='1d', start_time='', end_time='', count=-1, dividend_type='none', fill_data=True, data_dir=None)
get_full_tick(code_list)
get_divid_factors(stock_code, start_time='', end_time='')
```

### 数据下载类

```python
download_history_data(stock_code, period, start_time='', end_time='', incrementally=None)
download_history_data2(stock_list, period, start_time='', end_time='', callback=None, incrementally=None)
```

### 财务数据类

```python
get_financial_data(stock_list, table_list=[], start_time='', end_time='', report_type='report_time')
download_financial_data(stock_list, table_list=[])
download_financial_data2(stock_list, table_list=[], start_time='', end_time='', callback=None)
```

### 合约与板块信息类

```python
get_instrument_detail(stock_code, iscomplete=False)
get_instrument_type(stock_code)
download_sector_data()
get_sector_list()
get_stock_list_in_sector(sector_name, real_timetag=False)
get_index_weight(index_code)
download_index_weight()
```

### 交易日历与扩展信息

```python
get_trading_dates(market, start_time='', end_time='', count=-1)
get_trading_calendar(market, start_time='', end_time='')
get_holidays()
download_holiday_data()
get_trading_time(market, date='')
get_cb_info(stockcode)
download_cb_data()
get_etf_info()
download_etf_info()
get_ipo_info(start_time, end_time)
```

### 连接管理

```python
connect(ip='', port=0, session_id=0)
reconnect(ip='', port=0)
```

---

## xttrader 交易模块

### 构造与生命周期

```python
XtQuantTrader(path, session_id)
register_callback(callback)
start()
connect()
stop()
run_forever()
```

### 订阅

```python
subscribe(account)
unsubscribe(account)
```

### 下单/撤单

```python
order_stock(account, stock_code, order_type, order_volume, price_type, price, strategy_name='', order_remark='')
order_stock_async(account, stock_code, order_type, order_volume, price_type, price, strategy_name='', order_remark='')
cancel_order_stock(account, order_id)
cancel_order_stock_async(account, order_id)
cancel_order_stock_sysid(account, market, order_sysid)
cancel_order_stock_sysid_async(account, market, order_sysid)
fund_transfer(account, transfer_direction, price)
```

### 查询

```python
query_stock_asset(account)
query_stock_orders(account, cancelable_only=False)
query_stock_trades(account)
query_stock_positions(account)
query_stock_position(account, stock_code)
query_new_purchase_limit(account)
query_ipo_data()
query_account_infos()
query_account_status()
```

### 信用相关查询

```python
query_credit_detail(account)
query_stk_compacts(account)
query_credit_subjects(account)
query_credit_slo_code(account)
query_credit_assure(account)
```

### 回调接口（XtQuantTraderCallback）

```python
on_disconnected(self)
on_stock_order(self, order)
on_stock_trade(self, trade)
on_order_error(self, order_error)
on_cancel_error(self, cancel_error)
on_order_stock_async_response(self, response)
on_account_status(self, status)
```

---

## xtdatacenter（VIP连接扩展）

```python
from xtquant import xtdatacenter as xtdc

xtdc.set_token(token)
xtdc.set_allow_optmize_address(addr_list)
xtdc.set_kline_mirror_enabled(True)
xtdc.init(auto_start=True)
port = xtdc.listen(port=58621)
```

Use with:

```python
from xtquant import xtdata
xtdata.connect(port=port)
```

---

## Lookup Tips

- 查函数名：先在本文件定位模块和函数。
- 查参数细节：转到 `xtdata_api.md` / `xttrader_api.md`。
- 查字段含义：转到 `data_structures.md`。
- 找可运行模板：转到 `examples.md`。
