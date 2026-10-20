# 支付宝支付流程

## 概述

支付宝提供了一套完整的在线支付解决方案，涵盖 PC 网页支付、移动端支付、小程序支付等多种场景。核心采用 **支付接口 + 异步通知** 模式。

## 典型支付流程（统一收单下单并支付）

```
用户 → 商户系统 → 支付宝服务端 → 用户客户端 → 支付宝 → 商户系统
```

### 步骤

1. **下单** — 商户系统调用支付宝[统一收单下单并支付](https://opendocs.alipay.com/open/028r8t)接口，传入订单号、金额、商品信息等，获取 `trade_no`（支付宝交易号）。
2. **唤起支付** — 根据接口返回的 `form` 表单或 `trade_no`，在用户浏览器/App 内唤起支付宝收银台。
3. **用户支付** — 用户在支付宝客户端内完成密码/指纹验证。
4. **异步通知** — 支付宝服务端向商户配置的 `notify_url` 发送 POST 通知，携带 `trade_status`（交易状态）。
5. **同步跳转** — 支付完成后支付宝将用户浏览器/App 跳转回商户的 `return_url`（仅作为展示页面，不做金额校验）。
6. **查询确认** — 商户可调用[统一收单交易查询](https://opendocs.alipay.com/open/028x1e)接口确认最终状态。

### 关键状态

| 状态 | 说明 |
|------|------|
| WAIT_BUYER_PAY | 等待用户付款 |
| TRADE_CLOSED | 未付款交易超时关闭 / 已付款交易全额退款 |
| TRADE_SUCCESS | 交易支付成功 |
| TRADE_FINISHED | 交易结束（不可退款） |

### 常用接口

| 接口 | 用途 |
|------|------|
| alipay.trade.page.pay | PC 网页支付 |
| alipay.trade.wap.pay | 手机网页支付 |
| alipay.trade.app.pay | App 支付 |
| alipay.trade.create | 统一收单下单（仅创建，不唤起） |
| alipay.trade.query | 交易查询 |
| alipay.trade.refund | 交易退款 |
| alipay.trade.close | 交易关闭 |

## 参考

- [支付宝开放平台文档](https://opendocs.alipay.com/open/02e7gq)
