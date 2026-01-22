---
name: payment-help
description: >
  Analyzes user requirements and recommends the best Taiwan payment gateway solution.
  Provides comparison between NewebPay, ECPay, and PAYUNi based on specific needs.
  Triggers: "payment help", "金流推薦", "支付比較", "哪個金流", "which payment", "金流選擇"
user-invocable: true
license: MIT
metadata:
  author: paid-tw
  version: "2.0.0"
---

# 台灣金流選擇與推薦任務

你的任務是幫助用戶選擇最適合的台灣第三方支付金流服務。

## 任務流程

### Step 1: 了解用戶需求

詢問用戶以下問題：

1. **業務類型**：你的業務類型是什麼？
   - 一般電商（實體商品）
   - 數位商品/服務
   - SaaS 訂閱制
   - 實體店面 + 線上
   - 其他

2. **預計月交易量**：
   - < 100 筆
   - 100-1,000 筆
   - 1,000-10,000 筆
   - > 10,000 筆

3. **需要的功能**（可複選）：
   - 信用卡支付
   - LINE Pay
   - Apple Pay / Google Pay
   - ATM 轉帳
   - 超商代碼/條碼
   - 電子發票
   - 定期定額扣款

### Step 2: 分析並推薦

根據用戶需求，提供推薦：

## 金流比較分析

| 功能特色 | NewebPay 藍新 | ECPay 綠界 | PAYUNi 統一 |
|---------|--------------|-----------|-------------|
| **市場定位** | 中大型電商 | 台灣最大 | 統一集團 |
| **信用卡** | ✅ | ✅ | ✅ |
| **LINE Pay** | ✅ | ✅ | ✅ |
| **Apple Pay** | ✅ | ✅ | ✅ |
| **Google Pay** | ✅ | ✅ | ✅ |
| **ATM 轉帳** | ✅ | ✅ | ✅ |
| **超商代碼** | ✅ | ✅ | ✅ |
| **電子發票** | ❌ | ✅ 內建 | ❌ |
| **超商取貨付款** | ✅ | ✅ | ✅ |
| **定期定額** | ✅ | ✅ | ✅ |
| **API 文件** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| **系統穩定性** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

## 推薦決策樹

```
需要電子發票？
├─ 是 → 推薦 ECPay（內建發票功能）
└─ 否
   ├─ 高流量/穩定性優先？
   │  ├─ 是 → 推薦 NewebPay
   │  └─ 否
   │     ├─ 統一集團相關？
   │     │  ├─ 是 → 推薦 PAYUNi
   │     │  └─ 否 → 三者皆可，依偏好選擇
   └─ 快速上線？
      └─ 三者整合難度相近
```

### Step 3: 提供詳細建議

根據分析結果，提供：

1. **推薦的金流服務**及原因
2. **需要注意的事項**
3. **下一步行動**

## 情境推薦

### 需要電子發票
**推薦: ECPay**
- 金流 + 發票一站整合
- 減少開發工作量
- 使用 `/ecpay` 開始

### 高流量電商 / 穩定性優先
**推薦: NewebPay**
- 系統穩定性高
- API 文件完整
- 使用 `/newebpay` 開始

### 統一集團相關業務
**推薦: PAYUNi**
- 統一企業體系
- 與 7-11 系統整合佳
- 使用 `/payuni` 開始

### 快速上線 / 一般需求
**任一皆可**
- 整合難度相近
- 選擇文件風格順眼的
- 建議從 NewebPay 開始（文件最完整）

## 可用的 Skills

### NewebPay (藍新金流)

| Skill | 功能 | 使用方式 |
|-------|------|---------|
| `/newebpay` | 環境設定與總覽 | 開始新整合 |
| `/newebpay-checkout` | MPG 支付串接 | 建立支付功能 |
| `/newebpay-query` | 交易查詢 | 查詢訂單狀態 |
| `/newebpay-refund` | 退款處理 | 處理退款 |

### ECPay (綠界科技)

| Skill | 功能 | 使用方式 |
|-------|------|---------|
| `/ecpay` | 串接指南 | 開始 ECPay 整合 |

### PAYUNi (統一金流)

| Skill | 功能 | 使用方式 |
|-------|------|---------|
| `/payuni` | 串接指南 | 開始 PAYUNi 整合 |

## 使用範例

```bash
# 查看金流比較與推薦
/payment-help

# 開始 NewebPay 整合
/newebpay

# 直接串接支付功能
/newebpay-checkout 信用卡

# 查詢交易狀態
/newebpay-query

# 處理退款
/newebpay-refund
```

## 相關資源

- **Agent**: `payment-integrator` - 台灣金流串接專家
