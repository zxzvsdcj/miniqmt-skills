# XtQuant 常见问题与排查

## Table of Contents

- [1. 导入与环境问题](#1-导入与环境问题)
- [2. 连接与端口问题](#2-连接与端口问题)
- [3. 订阅与数据问题](#3-订阅与数据问题)
- [4. 交易与回调问题](#4-交易与回调问题)
- [5. 生产实践建议](#5-生产实践建议)

## 1. 导入与环境问题

### 问题：`No module named 'xtquant...` 或导入失败

**排查**：

1. 确认 Python 为 64 位
2. 确认 Python 版本在 `3.6~3.12`
3. 重新安装：`pip install --upgrade xtquant`
4. 检查运行环境是否与安装环境一致（虚拟环境/解释器路径）

## 2. 连接与端口问题

### 问题：`connect` 返回失败

**常见根因**：

- MiniQMT 未启动或未登录
- `path` 指向错误目录
- 同 `session_id` 短时间重复连接

**处理**：

- 先确认客户端登录状态
- 核对 `userdata_mini` / `userdata` 路径
- 对同一 `session_id` 重连时等待 > 3 秒

### 问题：端口 `58609` 被占用（xtdatacenter）

**处理**：

```python
from xtquant import xtdatacenter as xtdc

xtdc.set_token("YOUR_TOKEN")
xtdc.init(False)
port = xtdc.listen(port=58601)
print("port", port)
```

## 3. 订阅与数据问题

### 问题：订阅后没有回调

**检查项**：

- 是否调用了 `xtdata.run()` 保持进程阻塞
- 回调函数签名是否正确（`callback(datas)`）
- 订阅代码是否成功返回 `seq > 0`

### 问题：历史数据读取为空

**常见根因**：

- 未先下载历史数据
- 时间参数超出可用区间
- 代码格式不正确（应为 `000001.SZ`）

**处理**：

```python
from xtquant import xtdata

xtdata.download_history_data("000001.SZ", period="1d", incrementally=True)
data = xtdata.get_market_data_ex(["close"], ["000001.SZ"], period="1d", count=10)
print(data["close"])
```

### 问题：高并发订阅导致性能下降

**建议**：

- 单股订阅数量尽量控制
- 大范围行情优先 `subscribe_whole_quote`
- 回调内部仅做轻处理，耗时任务异步化

## 4. 交易与回调问题

### 问题：下单返回 `-1`

**检查项**：

- 账号是否已 `subscribe`
- 可用资金是否充足
- `order_volume` 是否符合规则（股票常见 100 股整数倍）
- 价格类型是否与当前交易环境匹配（模拟盘不一定支持全部市价类型）

### 问题：`order_remark` 被截断

**原因**：MiniQMT 客户端备注长度有限。

**建议**：

- 使用短备注
- 关键业务信息放外部订单映射表（`order_id -> metadata`）

### 问题：异步下单结果不一致/难追踪

**建议**：

- 记录 `seq`
- 同时监听：
  - `on_order_stock_async_response`
  - `on_stock_order`
  - `on_order_error`
- 使用统一订单状态机做落库与重放

## 5. 生产实践建议

- 回调里避免重计算和阻塞 IO。
- 建立重连机制：连接断开后重建 `XtQuantTrader` 并重新订阅账号。
- 统一日志结构：至少记录 `session_id`、`account_id`、`stock_code`、`order_id`、`seq`。
- 风控前置：
  - 时间窗校验（是否交易时段）
  - 资金与仓位约束
  - 下单频率限制
  - 错误重试上限
- 先模拟后实盘，实盘前进行回放测试与小资金灰度。
