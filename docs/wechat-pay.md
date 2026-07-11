# 微信支付流程

## 概述

微信支付提供 JSAPI 支付、小程序支付、App 支付、H5 支付、Native 支付等多种接入方式。核心采用 **下单 → 调起支付 → 支付结果通知** 模式（V3 版 API）。

## 典型支付流程（JSAPI 支付 / 小程序支付）

```
用户 → 商户系统 → 微信支付服务端 → 用户客户端 → 微信支付 → 商户系统
```

### 步骤

1. **获取用户标识** — 用户在商户网页/小程序授权后，获取 `openid`。
2. **下单** — 商户系统调用微信支付[JSAPI 下单](https://pay.weixin.qq.com/doc/v3/merchant/4012798957)接口（`POST /v3/pay/transactions/jsapi`），传入 `appid`、`mchid`、`openid`、订单号、金额等，获取 `prepay_id`（预支付交易会话标识）。
3. **调起支付** — 商户根据 `prepay_id` 生成签名参数（`appId`、`timeStamp`、`nonceStr`、`package`、`signType`），通过 JSAPI 调起微信支付收银台。
4. **用户支付** — 用户在微信客户端内完成密码/指纹验证。
5. **支付结果通知** — 微信支付服务端向商户配置的 `notify_url` 发送 POST 通知（密文结构），携带 `trade_state`（交易状态）。
6. **查询确认** — 商户可调用[查询订单](https://pay.weixin.qq.com/doc/v3/merchant/4012798910)接口（`GET /v3/pay/transactions/id/{transaction_id}`）确认最终状态。

### 关键状态

| 状态 | 说明 |
|------|------|
| NOTPAY | 未支付 |
| CLOSED | 已关闭 |
| REVOKED | 已撤销（仅付款码支付） |
| USERPAYING | 用户支付中 |
| SUCCESS | 支付成功 |
| REFUND | 转入退款 |

### 常用接口（V3）

| 接口 | 用途 |
|------|------|
| POST /v3/pay/transactions/jsapi | JSAPI 下单 |
| POST /v3/pay/transactions/app | App 下单 |
| POST /v3/pay/transactions/h5 | H5 下单 |
| POST /v3/pay/transactions/native | Native 下单 |
| GET /v3/pay/transactions/id/{transaction_id} | 查询订单 |
| POST /v3/refund/domestic/refunds | 申请退款 |
| POST /v3/pay/transactions/out-trade-no/{out_trade_no}/close | 关闭订单 |

## 参考

- [微信支付 V3 API 文档](https://pay.weixin.qq.com/doc/v3/merchant/)
