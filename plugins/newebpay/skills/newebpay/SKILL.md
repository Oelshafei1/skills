---
name: newebpay
description: 藍新金流(NewebPay)串接指南。使用於 "藍新", "NewebPay", "藍新金流", "藍新信用卡" 相關問題。提供 MPG 幕前支付、信用卡、LINE Pay、ATM、超商等支付整合。
license: MIT
metadata:
  author: paid-tw
  version: "1.0.0"
---

# 藍新金流整合指南

本 skill 提供藍新金流(NewebPay)線上交易幕前支付的完整技術串接指南，版本 NDNF-1.1.9。

## 快速開始

### 環境設定

**測試環境:**
- MPG交易: `https://ccore.newebpay.com/MPG/mpg_gateway`
- 商店代號: 從藍新金流測試帳號取得
- HashKey/HashIV: 從藍新金流測試環境取得

**正式環境:**
- MPG交易: `https://core.newebpay.com/MPG/mpg_gateway`
- 商店代號: 從藍新金流正式帳號取得
- HashKey/HashIV: 從藍新金流正式環境取得

### 支援的支付方式

- **信用卡**: 一次付清、分期付款、紅利折抵、銀聯卡、美國運通卡
- **行動支付**: Apple Pay、Google Pay、Samsung Pay
- **電子錢包**: LINE Pay、台灣Pay、BitoPay、TWQR
- **ATM**: WebATM、ATM轉帳
- **超商**: 代碼繳費、條碼繳費、取貨付款

## 核心流程

### 1. 發起交易 (MPG)

交易流程包含四個主要步驟:

1. **準備交易資料**: 組織訂單資訊、商品描述、金額等
2. **AES加密**: 使用商店的 HashKey 和 HashIV 進行 AES256 加密
3. **SHA256簽章**: 對加密後的資料進行 SHA256 簽章驗證
4. **Form Post**: 將資料以 HTML Form 方式 POST 到藍新金流

詳細實作請參閱 [references/mpg-transaction.md](references/mpg-transaction.md)

### 2. 接收回應

藍新金流會透過以下方式回傳結果:

- **NotifyURL**: 幕後通知，適合更新訂單狀態
- **ReturnURL**: 支付完成後導回商店頁面
- **CustomerURL**: 取號完成後導回(非即時支付)

詳細參數說明請參閱 [references/response-parameters.md](references/response-parameters.md)

### 3. 其他API功能

- **單筆交易查詢**: 查詢特定訂單的交易狀態
- **取消授權**: 取消信用卡授權
- **請退款**: 信用卡退款/取消退款
- **電子錢包退款**: LINE Pay、台灣Pay等電子錢包退款

## 加密解密

### AES256 加密

```php
// 加密範例
$key = "你的HashKey";
$iv = "你的HashIV";
$data = "要加密的JSON字串";

$encrypted = openssl_encrypt(
    $data,
    "AES-256-CBC",
    $key,
    OPENSSL_RAW_DATA,
    $iv
);
$encrypted_hex = bin2hex($encrypted);
```

### AES256 解密

```php
// 解密範例
$decrypted = openssl_decrypt(
    hex2bin($encrypted_hex),
    "AES-256-CBC",
    $key,
    OPENSSL_RAW_DATA|OPENSSL_ZERO_PADDING,
    $iv
);
// 移除 PKCS7 padding
$decrypted = strippadding($decrypted);
```

### SHA256 簽章

```php
// 簽章範例
$trade_info = "已加密的TradeInfo";
$hash_string = "HashKey={$key}&{$trade_info}&HashIV={$iv}";
$trade_sha = strtoupper(hash("sha256", $hash_string));
```

完整加解密實作請參閱 [scripts/encryption.php](scripts/encryption.php)

## 常見使用情境

### 情境1: 建立信用卡單次付款

```php
$params = [
    'MerchantID' => 'MS12345678',
    'RespondType' => 'JSON',
    'TimeStamp' => time(),
    'Version' => '2.3',
    'MerchantOrderNo' => 'ORDER_' . time(),
    'Amt' => 1000,
    'ItemDesc' => '商品名稱',
    'Email' => 'buyer@example.com',
    'CREDIT' => 1  // 啟用信用卡
];
```

### 情境2: 建立多元支付(含LINE Pay)

```php
$params = [
    // ... 基本參數 ...
    'CREDIT' => 1,      // 信用卡
    'LINEPAY' => 1,     // LINE Pay
    'VACC' => 1,        // ATM轉帳
    'CVS' => 1,         // 超商代碼
    'ReturnURL' => 'https://yourdomain.com/return',
    'NotifyURL' => 'https://yourdomain.com/notify'
];
```

### 情境3: 超商取貨付款

```php
$params = [
    // ... 基本參數 ...
    'CVSCOM' => 1,
    'LgsType' => 'B2C',  // B2C店到店 或 C2C個人寄件
    'CustomerURL' => 'https://yourdomain.com/cvs_result'
];
```

更多情境範例請參閱 [references/use-cases.md](references/use-cases.md)

## 參考資源

- [references/mpg-transaction.md](references/mpg-transaction.md) - MPG交易完整參數說明
- [references/response-parameters.md](references/response-parameters.md) - 回應參數詳細說明
- [references/error-codes.md](references/error-codes.md) - 錯誤代碼對照表
- [references/use-cases.md](references/use-cases.md) - 常見使用情境範例
- [references/troubleshooting.md](references/troubleshooting.md) - 疑難排解
- [scripts/encryption.php](scripts/encryption.php) - 加解密完整實作

## 重要注意事項

1. **安全性**
   - HashKey 和 HashIV 必須妥善保管，不可暴露在前端
   - 所有交易資料都必須經過 AES256 加密
   - 建議使用 NotifyURL 在後端接收交易結果

2. **時間戳記**
   - TimeStamp 必須是 Unix timestamp (秒數)
   - 容許誤差值為 ±120 秒
   - 確保伺服器時間正確

3. **訂單編號**
   - MerchantOrderNo 在同一商店中不可重複
   - 長度限制30字元
   - 限英文、數字、底線

4. **測試注意事項**
   - 測試環境與正式環境的 URL、商店代號、金鑰都不同
   - 測試卡號請參考官方測試文件
   - 超商物流測試需要實際產生物流單號

5. **Port 限制**
   - ReturnURL、NotifyURL、CustomerURL 只接受 80 和 443 Port

## 疑難排解

常見問題請參閱 [references/troubleshooting.md](references/troubleshooting.md)

## 相關 Skills

- `/payment-help` - 查看所有可用的支付 skills
- `/ecpay` - 綠界科技串接
- `/payuni` - PAYUNi 統一金流串接
