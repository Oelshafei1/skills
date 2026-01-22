---
name: newebpay
description: >
  Guides user through NewebPay integration setup including environment configuration,
  credential setup, and skill routing. Use when starting NewebPay integration or needing general guidance.
  Triggers: "newebpay", "藍新", "NewebPay", "藍新金流", "newebpay help", "藍新串接教學"
argument-hint: "[功能: checkout/query/refund]"
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Grep
  - Glob
user-invocable: true
license: MIT
metadata:
  author: paid-tw
  version: "2.0.0"
---

# 藍新金流整合任務

你的任務是幫助用戶設定藍新金流(NewebPay)環境並引導至適當的串接功能。

## 任務流程

### Step 1: 分析用戶需求

用戶輸入: `$ARGUMENTS`

根據用戶需求，判斷下一步：
- 若包含「串接」「checkout」「建立交易」「MPG」→ 引導使用 `/newebpay-checkout`
- 若包含「查詢」「query」「訂單狀態」→ 引導使用 `/newebpay-query`
- 若包含「退款」「refund」「取消」→ 引導使用 `/newebpay-refund`
- 若無特定指定 → 執行 Step 2 環境設定檢查

### Step 2: 環境設定檢查

詢問用戶以下問題：

1. **專案框架**：你使用什麼框架？
   - PHP (Laravel / 原生 PHP / 其他)
   - Node.js (Express / NestJS / 原生 / 其他)
   - Python (Django / Flask / FastAPI / 其他)
   - 其他

2. **環境狀態**：是否已有藍新金流商店帳號？
   - 是，已有測試環境帳號
   - 是，已有正式環境帳號
   - 否，需要申請

### Step 3: 建立環境設定

根據用戶的框架，建立環境變數設定：

**環境變數範本:**
```bash
NEWEBPAY_MERCHANT_ID=MS12345678
NEWEBPAY_HASH_KEY=your_hash_key
NEWEBPAY_HASH_IV=your_hash_iv
NEWEBPAY_ENV=test  # test 或 production
```

**指導用戶:**
1. 在專案根目錄建立或編輯 `.env` 檔案
2. 加入上述環境變數
3. 確保 `.env` 已加入 `.gitignore`

### Step 4: 建立加密模組

根據用戶框架，提供對應的加密解密函式：

**PHP:**
```php
function encrypt($data, $key, $iv) {
    $encrypted = openssl_encrypt(
        $data,
        "AES-256-CBC",
        $key,
        OPENSSL_RAW_DATA,
        $iv
    );
    return bin2hex($encrypted);
}

function decrypt($encrypted_hex, $key, $iv) {
    return rtrim(openssl_decrypt(
        hex2bin($encrypted_hex),
        "AES-256-CBC",
        $key,
        OPENSSL_RAW_DATA | OPENSSL_ZERO_PADDING,
        $iv
    ), "\x00..\x1F");
}

function generateSha($trade_info, $key, $iv) {
    return strtoupper(hash("sha256", "HashKey={$key}&{$trade_info}&HashIV={$iv}"));
}
```

**Node.js:**
```javascript
const crypto = require('crypto');

function encrypt(data, key, iv) {
  const cipher = crypto.createCipheriv('aes-256-cbc', key, iv);
  let encrypted = cipher.update(data, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  return encrypted;
}

function decrypt(encrypted, key, iv) {
  const decipher = crypto.createDecipheriv('aes-256-cbc', key, iv);
  let decrypted = decipher.update(encrypted, 'hex', 'utf8');
  decrypted += decipher.final('utf8');
  return decrypted.replace(/[\x00-\x1F]+$/g, '');
}

function generateSha(tradeInfo, key, iv) {
  return crypto.createHash('sha256')
    .update(`HashKey=${key}&${tradeInfo}&HashIV=${iv}`)
    .digest('hex')
    .toUpperCase();
}
```

### Step 5: 引導下一步

完成環境設定後，根據用戶需求引導：

| 需求 | Skill | 說明 |
|------|-------|------|
| 建立支付頁面 | `/newebpay-checkout` | MPG 幕前支付串接 |
| 查詢交易狀態 | `/newebpay-query` | 交易查詢 API |
| 處理退款 | `/newebpay-refund` | 信用卡/電子錢包退款 |

## 環境資訊

| 環境 | API Base URL |
|------|--------------|
| 測試 | `https://ccore.newebpay.com` |
| 正式 | `https://core.newebpay.com` |

## 支援的支付方式

- **信用卡**: 一次付清、分期付款、紅利折抵
- **行動支付**: Apple Pay、Google Pay、Samsung Pay
- **電子錢包**: LINE Pay、台灣Pay、TWQR
- **ATM**: WebATM、ATM轉帳
- **超商**: 代碼繳費、條碼繳費

## 重要注意事項

1. HashKey 和 HashIV 必須保密，不可暴露在前端
2. 時間戳記容許誤差 ±120 秒
3. 訂單編號不可重複，限 30 字元
4. ReturnURL/NotifyURL 只接受 80/443 Port
