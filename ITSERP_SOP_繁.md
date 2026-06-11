# ITSERP 標準操作規程 (SOP)

> **系統名稱**：ITSERP（C3 服裝貿易管理系統）  
> **適用版本**：.NET Framework 4.8 / WinForms  
> **更新日期**：2026-06-11  
> **文檔版本**：v1.0  

---

## 目錄

1. [端到端業務流程總覽](#0-端到端業務流程總覽)
2. [文檔類型體系](#0-文檔類型體系)
3. [系統登錄與基礎設置](#1-系統登錄與基礎設置)
4. [基礎數據維護](#2-基礎數據維護)
5. [採購管理](#3-採購管理)
6. [銷售管理](#4-銷售管理)
7. [倉庫/庫存管理](#5-倉庫庫存管理)
8. [財務管理](#6-財務管理)
9. [利潤分析](#7-利潤分析)
10. [報表系統](#8-報表系統)
11. [系統管理](#9-系統管理)
12. [附錄](#附錄)

---

## 0. 端到端業務流程總覽

### 0.1 完整業務流程圖

ITSERP（C3）的完整業務流程從 **客戶下單** 到 **貨物交付**，涉及 4 個角色協作：

```
                    ┌──────────────┐
                    │ 客戶下達 CPO  │
                    └──────┬───────┘
                           │ ① 獲得客戶採購訂單
                    ┌──────▼───────┐
                    │  申請 Item   │  ← 在 Kintone 創建產品編碼
                    │  master      │    負責人：Assistant
                    └──────┬───────┘
                           │ ②
                    ┌──────▼───────┐
                    │ 創建 PI      │  ← 在 C3 創建形式發票給客戶
                    │ (Proforma    │    負責人：Assistant
                    │  Invoice)    │
                    └──────┬───────┘
                           │ ③
                    ┌──────▼───────┐
                    │ 收款         │  ← 會計部收到客戶貨款
                    │ Payment      │    負責人：Account
                    │ Received     │
                    └──────┬───────┘
                           │ ④
                    ┌──────▼───────┐
                    │ 創建 PO      │  ← 在 C3 向供應商下采購單
                    │ (Purchase    │    負責人：Assistant
                    │  Order)      │
                    └──────┬───────┘
                           │ ⑤
                    ┌──────▼───────┐
                    │ 付款         │  ← 會計部向供應商付款
                    │ Payment      │    負責人：Account
                    │ Sent         │
                    └──────┬───────┘
                           │ ⑥
                    ┌──────▼───────┐
                    │ 貨物到達倉庫  │  ← 供應商送貨至【天安倉庫】
                    │ Delivery     │    負責人：Warehouse Keeper
                    │ Arrival      │
                    └──────┬───────┘
                           │ ⑦
                    ┌──────▼───────┐
                    │ 入庫 Inbound │  ← 在 C3 創建入庫單
                    │              │    負責人：Warehouse Assistant
                    └──────┬───────┘
                           │ ⑧
                    ┌──────▼───────┐
                    │ 庫存確認      │  ← 3個條件全部正確纔可確認
                    │ Stock Confirm│    負責人：Assistant
                    └──────┬───────┘
                           │ ⑨
                    ┌──────▼───────┐
                    │ 發貨指令 DO  │  ← 在 C3 下達發貨指令給倉庫
                    │ Delivery     │    負責人：Assistant
                    │ Order        │
                    └──────┬───────┘
                           │ ⑩
                    ┌──────▼───────┐
                    │ 出庫 Outbound│  ← 貨物出庫交付客戶
                    │              │    負責人：Warehouse Assistant
                    └──────────────┘
```

### 0.2 角色職責矩陣

| 角色 | 負責步驟 | 所屬部門 | 系統操作 |
|------|----------|----------|----------|
| **Assistant（跟單員）** | CPO → Item → PI → PO → Stock Confirm → DO | 業務部 | C3 + Kintone |
| **Account（會計）** | 收款（客戶） + 付款（供應商） | 財務部 | C3 財務模塊 |
| **Warehouse Keeper（倉管）** | 收貨通知 | 倉儲部 | 現場確認 |
| **Warehouse Assistant（倉務員）** | Inbound → Outbound | 倉儲部 | C3 倉庫模塊 |

### 0.3 關鍵業務單據流轉

```
CPO (客戶採購訂單)
  │
  ▼
PI  (形式發票，發給客戶)
  │
  ▼
PO  (採購訂單，發給供應商)
  │
  ▼
Inbound (入庫單，供應商送貨後)
  │
  ▼
DO  (發貨指令)
  │
  ▼
Outbound (出庫，交付客戶)
```

> ⚠️ **重要流程次序**：必須先創建 **PI**（發給客戶），再創建 **PO**（發給供應商）。PI 相當於向客戶的報價/預發票，PO 是向供應商的採購指令。

---

## 0. 文檔類型體系

### 0.1 文檔類型一覽表

系統涉及的完整文檔類型體系（基於 Shimada 文檔分類標準）：

#### 📄 銷售類文檔 (Sales Document)

| 文檔類型 | 縮寫 | 說明 | 方向 | 負責人 |
|----------|------|------|------|--------|
| Customer Purchase Order | **CPO** | 客戶採購訂單 | 客戶→我方 | Assistant |
| Proforma Invoice | **PI** | 形式發票/報價單 | 我方→客戶 | Assistant |
| Sales Invoice | **SI** | 銷售發票 | 我方→客戶 | Account |
| Sales Return | **SR** | 銷售退貨單 | 客戶→我方 | Assistant |
| Credit Note | **CB** | 貸記單 | 我方→客戶 | Account |
| Debit Note | **DB** | 借記單 | 我方→客戶 | Account |
| Sample and Other Cost | **OC** | 樣板/其他費用單 | 供應商→我方 | Assistant |

#### 📥 採購類文檔 (Purchase Document)

| 文檔類型 | 縮寫 | 說明 | 方向 | 負責人 |
|----------|------|------|------|--------|
| Purchase Order | **PO** | 採購訂單 | 我方→供應商 | Assistant |
| BOM Purchase Order | **BPN** | BOM 採購單（物料清單採購） | 我方→供應商 | Assistant |
| Supplier Invoice | — | 供應商發票 | 供應商→我方 | Account |
| Purchase Return | **PR** | 採購退貨單 | 我方→供應商 | Assistant |
| Shipment Sample | — | 船樣/寄樣記錄 | 供應商→我方 | Assistant |

#### 🚢 船務類文檔 (Shipping Document)

| 文檔類型 | 縮寫 | 說明 | 負責人 |
|----------|------|------|--------|
| Export Invoice | **EI** | 出口發票（報關用） | Shipping |
| Sales Invoice | **SI** | 銷售發票 | Account/Shipping |

#### 📦 發貨單配套文件 (DO Doc Check)

| 文檔類型 | 縮寫 | 說明 |
|----------|------|------|
| Air Waybill | **AWB** | 空運提單 |
| Bill of Lading | **B/L** | 海運提單 |
| Packing List | **PL** | 裝箱單 |
| Shipping Order | **SO** | 裝運單 |
| Cargo Receipt | **CR** | 貨物收據 |
| Delivery Note | **DL** | 交貨單 |
| Certificate / Customs Declaration | **C/D** | 產地證/報關單 |
| Others | — | 其他文件 |

### 0.2 文檔號命名規則（參考）

| 文檔類型 | 編號格式 | 示例 |
|----------|----------|------|
| CPO | CPO-XXX | CPO-001 |
| PI | PI-XXX | PI-001 |
| PO | PO-XXX | PO-001 |
| BPN | BPN-XXX | BPN-001 |
| SR | SR-XXXX | SR-0213 |
| CB | CB-XXX | CB-001 |
| DB | DB-XXX | DB-001 |
| OC | OC-XXX | OC-001 |
| EI | EI-XXX | EI-001 |
| SI | SI-XXXXX | SI-12345 |
| PL | PL-XXX | PL-001 |
| DO | DO-XXX | DO-001/002 |

---

## 1. 系統登錄與基礎設置

### 1.1 登錄系統

| 步驟 | 操作 | 說明 |
|------|------|------|
| 1 | 雙擊運行 **ITSERP.exe** | 啓動應用程序 |
| 2 | 在登錄窗口輸入 **工號** 和 **密碼** | 默認登錄號：`1050`，默認密碼：`spJun5` |
| 3 | 選擇 **服務器地址** | 自動從配置讀取（192.168.222.99） |
| 4 | 勾選 **記住密碼（Remember）**（可選） | 下次自動填充 |
| 5 | 點擊 **Login（登錄）** | 登錄系統 |

### 1.2 切換語言

系統支持 **英文（en-US）** 和 **日文（ja-JP）**：

| 步驟 | 操作 |
|------|------|
| 1 | 進入系統設置界面 |
| 2 | 在 **Language** 下拉框中選擇目標語言 |
| 3 | 保存設置，重啓系統生效 |

### 1.3 切換配色方案

| 步驟 | 操作 |
|------|------|
| 1 | 進入系統設置 |
| 2 | 在 **ColorScheme** 中選擇預設配色 |
| 3 | 保存後立即生效 |

---

## 2. 基礎數據維護

### 2.1 客戶管理（Customer）

**目的**：維護客戶基本信息，包括名稱、地址、聯繫方式、幣別等。

| 步驟 | 操作 | 快捷鍵/按鈕 |
|------|------|-------------|
| 1 | 進入客戶維護界面 | 菜單 → 基礎數據 → 客戶 |
| 2 | 點擊 **新增（Add）** | `btnAdd` |
| 3 | 填寫客戶信息： | |
| | - **公司名稱（CompanyName）** | 必填 |
| | - **英文名稱（EnglishName）** | 外貿客戶必填 |
| | - **公司類型（CompanyType）** | 選擇客戶/供應商 |
| | - **聯繫電話（Tel）** | |
| | - **傳真（Fax）** | |
| | - **地址/郵編（PostCode）** | |
| | - **網站（Website）** | |
| | - **法律主體（Legal）** | |
| 4 | 點擊 **保存（Save）** | `btnSave` |
| 5 | 如需修改，選擇記錄後點擊 **修改（Modify）** | `btnEdit` 或 `btnBaseModify` |
| 6 | 如需刪除，選擇記錄後點擊 **刪除（Delete）** | `btnDelete` |

### 2.2 供應商管理（Supplier）

**目的**：維護供應商信息，與客戶管理類似，但帶有供應商特定字段。

| 步驟 | 操作 |
|------|------|
| 1 | 進入供應商維護界面 |
| 2 | 使用 `cmbSupplier` 選擇或搜索已有供應商 |
| 3 | 點擊 **Add（新增）** 添加新供應商 |
| 4 | 填寫供應商信息（名稱、料號前綴、聯繫人等） |
| 5 | 點擊 **Save（保存）** |

### 2.3 產品/款號管理（Product / Apparel）

**目的**：維護成衣款號（Apparel）、物料編碼（ItemCode）及對應的供應商料號。

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 進入產品維護界面 | |
| 2 | 在 `lblProductNO` 字段輸入 **產品編號** | |
| 3 | 在 `lblProductName` 字段輸入 **產品名稱** | |
| 4 | 選擇 **成衣部門（ApparelDepartment）** | `cmbApparelDepartment` |
| 5 | 設置 **顏色（Color）** 和 **供應商顏色（VendorColor）** | 支持批量粘貼（`btnPasteColor`、`btnPasteVendorColor`） |
| 6 | 關聯 **供應商料號（VendorParts）** | |
| 7 | 上傳產品圖片 | `btnImgAdd` / `btnImgView` / `btnImgDelete` |
| 8 | 上傳產品附件 | `btnAddFile` / `btnDelFile` |
| 9 | 點擊 **保存（Save）** | |

### 2.4 銷售員/買手/經理維護

| 步驟 | 操作 |
|------|------|
| 1 | 進入人事/角色維護界面 |
| 2 | 維護 **Salesman（銷售員）** — 如 Yan Leung |
| 3 | 維護 **Buyer（買手）** |
| 4 | 維護 **Manager（經理）** |
| 5 | 維護 **Assistance（跟單員）** — 如 Stella Wong |

---

## 3. 採購管理

### 3.1 創建採購訂單（PO）

**目的**：向供應商下達成衣/物料採購訂單。

| 步驟 | 操作 | 字段/按鈕 |
|------|------|-----------|
| 1 | 進入 **採購訂單（Purchase Order）** 界面 | |
| 2 | 點擊 **新增（Add）** | `btnAdd` |
| 3 | 輸入訂單頭信息： | |
| | - **PO編號（txtOrderNO）** | 系統自動生成或手動輸入 |
| | - **訂單日期（dtOrderDate）** | 默認當天 |
| | - **公司（cmbCompany）** | 選擇採購公司實體 |
| | - **供應商（Supplier）** | `cmbSupplier` / `btnSupplier` |
| | - **供應商名稱** | 自動帶出 |
| | - **幣別（cmbCurrency）** | 如 HKD、USD |
| | - **付款方式（cmbPayment）** | |
| | - **倉庫（cmbWarehouse）** | 指定入庫倉庫 |
| | - **業務員（cmbSaler）** | |
| | - **買手（cmbBuyer）** | |
| | - **經理（cmbManager）** | |
| | - **成衣款號（cmbApparel）** | |
| | - **成衣部門（cmbApparelDepartment）** | |
| 4 | 輸入明細行（PO Line Items）： | |
| | - **產品編碼（ItemCode）** | |
| | - **供應商料號（VendorParts）** | |
| | - **尺寸（Size）** | |
| | - **顏色（Color / VendorColor）** | |
| | - **數量（QTY）** | |
| | - **單價（Price）** | |
| 5 | 點擊 **添加記錄（AddRecord）** 添加到明細表 | `btnAddRecord` |
| 6 | 重複第4-5步添加所有明細行 | |
| 7 | 點擊 **保存（Save）** 保存整單 | `btnSave` |

### 3.2 修改/審覈採購訂單

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 查詢需要修改的PO | `btnQuery` |
| 2 | 點擊 **修改（Edit）** | `btnEdit` |
| 3 | 修改訂單信息 | |
| 4 | 點擊 **保存修改（ModifySave）** | `btnModifySave` |
| 5 | 審覈通過後點擊 **審覈（Check）** | `btnCheck` |
| 6 | 如需反審覈，點擊 **取消審覈（UnCheck）** | `btnUnCheck` |

### 3.3 採購收貨

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 打開對應採購訂單 | |
| 2 | 點擊 **收貨（OutStock）** 或相關入庫操作 | `btnOutStock` |
| 3 | 確認實收數量 | |
| 4 | 打印入庫單 | `btnPrint` |

### 3.4 採購訂單變更（Revised PO）

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇需要變更的PO | |
| 2 | 點擊 **修訂（Revised）** | `btnRevised` |
| 3 | 記錄變更原因（txtReason） | |
| 4 | 提交變更，系統保留變更歷史 | |

### 3.5 導入採購訂單

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 準備標準Excel模板（SmdPO.xlsx） | |
| 2 | 點擊 **導入（Import）** | `btnImport` |
| 3 | 選擇Excel文件 | |
| 4 | 系統驗證數據格式後導入 | |

### 3.6 採購訂單導出

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 查詢目標PO列表 | |
| 2 | 點擊 **導出（Export）** | `btnExport` 或 `DataGridViewToExcel` |
| 3 | 選擇保存路徑 | |
| 4 | 導出爲Excel格式 | |

---

## 4. 銷售管理

### 4.0 銷售端總流程

```
CPO (客戶採購訂單) ──→ PI (形式發票) ──→ 客戶確認 + 收款 ──→ 正式執行
```

### 4.1 客戶採購訂單處理（CPO）

**目的**：接收並處理客戶下達的採購訂單（如 New Balance 的 CPO-001）。

| 步驟 | 操作 | 負責人 | 說明 |
|------|------|--------|------|
| 1 | 從客戶處獲取 **CPO（Customer Purchase Order）** | Assistant | 通常通過 Email/EDI 接收 |
| 2 | 檢查 CPO 內容：產品款號、數量、單價、交期、付款條件 | Assistant | 確認與報價一致 |
| 3 | 如 CPO 中有新產品未在系統中登記→ 跳轉到 **Kintone** 申請 Item master | Assistant | Kintone 是獨立系統 |
| 4 | 在 C3 系統中查找或創建產品信息 | Assistant | 確保 Item Code 與 CPO 一致 |
| 5 | 記錄 CPO 編號（如 CPO-001）用於後續 PI/PO 關聯 | Assistant | 所有下游單據關聯此編號 |

### 4.2 創建形式發票（PI — Proforma Invoice）

**目的**：在 C3 系統創建 PI 發給客戶，作爲交易確認和收款依據。

| 步驟 | 操作 | 字段/按鈕 | 負責人 |
|------|------|-----------|--------|
| 1 | 進入 **PI（形式發票）** 界面 | | Assistant |
| 2 | 點擊 **新增（Add）** | `btnAdd` | |
| 3 | 輸入 PI 頭部信息： | | |
| | - **PI 編號** | 關聯 CPO 編號生成 |
| | - **PI 日期（Document Date）** | 默認當天 |
| | - **文檔級別（Document Level）** | General / Director |
| | - **客戶（Customer）** | 從 CPO 獲取 |
| | - **客戶 PO 號（Customer PO Number）** | 填入 CPO 編號 |
| | - **成衣款號（Apparel Name）** | |
| | - **幣別（Currency）** | |
| | - **付款方式（Payment Terms）** | |
| | - **業務員（Salesman）** | |
| | - **跟單員（Assistant）** | |
| 4 | 輸入 PI 明細行： | | |
| | - **產品/款號（Item Code）** | |
| | - **數量（QTY）** | |
| | - **單價** | |
| | - **金額** | |
| 5 | 點擊 **保存（Save）** | `btnSave` | |
| 6 | 將 PI 發送給客戶確認 | Email | |
| 7 | 客戶確認後→**會計收款** | Account | |
| 8 | 收款完成→正式執行→**創建 PO** | Assistant | 跳轉 §3 採購管理 |

**PI 支持的分支文檔類型**：

| 類型 | 說明 | 觸發條件 |
|------|------|----------|
| Proforma Invoice (PI) | 標準形式發票 | 客戶確認後 |
| Sales Return (SR) | 銷售退貨 | 客戶退貨時 |
| Credit Note (CB) | 貸記單 | 調整/優惠 |
| Debit Note (DB) | 借記單 | 額外費用 |
| Sample/Other Cost (OC) | 樣板及其他費用 | 樣板/特殊費用 |

### 4.3 創建銷售訂單（SO）

**目的**：處理來自客戶（如 New Balance）的銷售訂單。

| 步驟 | 操作 | 字段/按鈕 |
|------|------|-----------|
| 1 | 進入 **銷售訂單（Sales Order）** 界面 | |
| 2 | 點擊 **新增（Add）** | `btnAdd` |
| 3 | 輸入訂單頭信息： | |
| | - **訂單編號（txtOrderNO）** | 自動生成 |
| | - **客戶PO號（CustomerPO/NO）** | 客戶提供的PO號 |
| | - **訂單日期（dtOrderDate）** | |
| | - **客戶（cmbCustomer）** | 選擇 |`btnCustomer` 快速選擇 |
| | - **公司（cmbCompany）** | |
| | - **幣別（cmbCurrency）** | |
| | - **付款方式（cmbPayment）** | |
| | - **交貨公司（cmbDeliveryCompany）** | |
| | - **交貨地址（txtDeliveryAddress）** | 可點擊 `btnGetAddress` 獲取 |
| | - **業務員（cmbSaler）** | |
| | - **買手（cmbBuyer）** | |
| | - **經理（cmbManager）** | |
| | - **倉庫（cmbWarehouse）** | |
| | - **成衣款號（cmbApparel）** | |
| | - **成衣部門（cmbApparelDepartment）** | |
| 4 | 輸入明細行： | |
| | - **產品編號/名稱** | |
| | - **數量（QTY）** | |
| | - **單價** | 可點擊 `btnGetPrice` / `btnContextPrice` 獲取價格 |
| | - **毛重/淨重/體積（GW/NW/CBM）** | 用於裝箱計算 |
| | - **箱號（CTN）、箱嘜（BoxCode/CaseMark）** | |
| 5 | 如有多個明細，逐行添加 | `btnAddRecord` |
| 6 | 輸入 **備註（txtMemo）** | |
| 7 | 點擊 **保存（Save）** | `btnSave` |

### 4.2 銷售訂單審覈與QC

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇待審覈的SO | |
| 2 | 點擊 **審覈（Check）** | `btnCheck` |
| 3 | 如需要QC：勾選 **IsQC**，輸入 **QC日期** 和 **QC備註** | `chkIsQC` / `chkIsRevised` |
| 4 | 如需要急單標誌：勾選 **UrHent** 並輸入說明 | `chkUrHent` / `txtUrHent` |
| 5 | 審覈後系統記錄 **審覈人（CheckUser）** 和 **審覈日期（CheckDate）** | 自動填充 |

### 4.3 銷售訂單變更

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 查詢需要變更的SO | `btnQuery` |
| 2 | 點擊 **修訂（Revised）** | `btnRevised` |
| 3 | 修改交貨日期（dtDeliveryDate / txtRevisedDate） | |
| 4 | 記錄變更原因 | `txtReason` |
| 5 | 保存變更 | |

### 4.4 裝運與裝箱（Packing List）

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇已審覈的SO | |
| 2 | 點擊 **裝箱清單（PackingList）** | `btnPackingList` |
| 3 | 輸入裝箱數據： | |
| | - **箱數（SumCtns）** | |
| | - **總毛重（SumGW）** | |
| | - **總淨重（SumNW）** | |
| | - **總體積（SumCMB / CBM）** | |
| | - **包裝箱類型（cmbPackageCartons）** | |
| | - **重量單位（cmbWeightUnit）** | |
| 4 | 點擊 **打印裝箱單** | `btnPrint` / `btnPrintShipmentSample` |

### 4.5 發貨/出庫（Out Stock）

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇待發貨的SO | |
| 2 | 點擊 **出庫（OutStock）** | `btnOutStock` |
| 3 | 確認發貨數量 | |
| 4 | 確認物流公司（cmbCourierPaidBy） | |
| 5 | 打印 **DCB（交貨單）** | `btnPrint_DCB` |
| 6 | 更新訂單狀態 | |

### 4.6 導入銷售訂單

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 準備標準格式的銷售訂單Excel | |
| 2 | 點擊 **導入（ImportSO）** | `btnImportSO` / `btnImportSOList` |
| 3 | 選擇文件並確認導入 | |
| 4 | 系統校驗後批量創建SO | |

### 4.7 客戶PO管理

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇關聯的SO | |
| 2 | 點擊 **添加客戶PO（AddCustomerPO）** | `btnAddCustomerPO` |
| 3 | 輸入客戶PO號及對應明細 | |
| 4 | 點擊 **刷新客戶PO（RefreshCustomerPO）** 同步更新 | `btnRefreshCustomerPO` |

---

## 5. 倉庫/庫存管理

### 5.1 庫存查詢

| 步驟 | 操作 | 按鈕/字段 |
|------|------|-----------|
| 1 | 進入庫存查詢界面 | |
| 2 | 選擇 **倉庫（cmbWarehouse）** | |
| 3 | 輸入 **產品編號（lblProductNO）** 或名稱 | |
| 4 | 點擊 **查詢（Query）** | `btnQuery` |
| 5 | 查看庫存數量（stockQtyDataGridViewTextBoxColumn） | |

### 5.2 庫存導入

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 準備庫存Excel文件 | |
| 2 | 點擊 **導入庫存（ImportStock）** | `btnImportStock` |
| 3 | 選擇文件並導入 | |

### 5.3 庫存調整

| 步驟 | 操作 |
|------|------|
| 1 | 查詢目標庫存記錄 |
| 2 | 點擊 **修改（Edit）** |
| 3 | 調整數量、倉位等信息 |
| 4 | 保存並記錄調整原因 |

### 5.4 庫存報表

| 步驟 | 操作 |
|------|------|
| 1 | 進入庫存報表界面 |
| 2 | 選擇報告類型 |
| 3 | 設置篩選條件（倉庫、產品範圍、日期） |
| 4 | 點擊生成報告並打印/導出 |

---

## 6. 財務管理

### 6.1 應收賬款管理（AR）

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 進入 **應收管理** 界面 | |
| 2 | 查詢客戶未結清的銷售訂單/發票 | `btnQuery` |
| 3 | 查看 **AR Aging（賬齡分析）** | `btnARsAgingReport` |
| 4 | 對到期款項進行催收 | |

### 6.2 應付賬款管理（AP）

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 進入 **應付管理** 界面 | |
| 2 | 查詢供應商未結清的採購訂單 | |
| 3 | 查看 **AP Aging（賬齡分析）** | `btnAPsAgingReport` |
| 4 | 安排付款計劃 | |

### 6.3 收款錄入

| 步驟 | 操作 | 字段 |
|------|------|------|
| 1 | 進入 **收款（Payment Received）** 界面 | |
| 2 | 選擇客戶 | `cmbCustomer` |
| 3 | 輸入收款金額 | `lblAmount` / `lblAmountHKD` |
| 4 | 選擇 **銀行（cmbBank）** | |
| 5 | 輸入 **支票號碼（txtChequeNo）**（如爲支票） | |
| 6 | 選擇幣別和匯率 | `cmbCurrency` |
| 7 | 輸入 **AP日期（dtAPDate）** | |
| 8 | 點擊 **保存** | `btnSave` |
| 9 | 打印收款收據 | `btnPrint` |

### 6.4 發票管理

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇已出庫的SO | |
| 2 | 點擊 **發票（Invoice）** | `btnInvoice` |
| 3 | 系統生成發票編號（txtInvoiceNO） | |
| 4 | 確認發票金額和明細 | |
| 5 | 點擊 **打印發票（Print_Invoice）** | `btnPrint_Invoice` |
| 6 | 發送電子發票給客戶 | `btnEmail` |

---

## 7. 利潤分析

### 7.1 通用利潤報告

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 進入利潤分析模塊 | |
| 2 | 選擇報告類型：**通用利潤（ProfitReport）** | `btnProfitReport` |
| 3 | 設置篩選條件： | |
| | - **年份（cmbYears）** | |
| | - **月份（cmbMonths）** | |
| 4 | 點擊 **查詢（Query）** 生成報告 | |
| 5 | 查看毛利率（GP）| `lblGP` |

### 7.2 按銷售員利潤分析

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇 **ProfitReport_Salesman** | `btnProfitReport_Salesman` |
| 2 | 選擇 **銷售員（cmbSalesman）** 進行篩選 | |
| 3 | 系統彙總每個銷售員的： | |
| | - 銷售額（Sales） | |
| | - 採購成本（Purchase） | |
| | - 數量（Qty） | |
| | - 毛利率（G/P） | |

### 7.3 按客戶利潤分析

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇 **Profit（customer, sales, G/P）** | |
| 2 | 選擇客戶範圍 | |
| 3 | 查看客戶維度的利潤貢獻 | |

### 7.4 按款號/產品利潤分析

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇 **ProfitReport_Apparel** | `btnProfitReport_Apparel` |
| 2 | 查看每個款號的銷售額、成本、毛利率 | |

### 7.5 期間利潤對比

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇 **ProfitReportRangeCompare** | `btnProfitReportRangeCompare` |
| 2 | 設置兩個對比期間（如本月 vs 上月，今年 vs 去年） | |
| 3 | 系統生成對比報表，展示增減變化 | |

### 7.6 全成本利潤分析

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇 **ProfitReportSummaryAllCost** | `btnProfitReportSummaryAllCost` |
| 2 | 系統計入所有成本項目（base costs, FC account items） | |
| 3 | 查看扣除全部成本後的淨利潤 | |

### 7.7 Ranking 排名分析

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 選擇 **Ranking 分析** 報告 | |
| 2 | 選擇排名維度： | |
| | - **按產品（Ranking item）** | |
| | - **按客戶（Ranking customer）** | |
| | - **按供應商（Ranking vendor）** | |
| | - **按業務員（Ranking salesman）** | |
| 3 | 查看 TOP 排名數據（數量、金額） | |

---

## 8. 報表系統

### 8.1 常用報表一覽

| 報表名稱 | 按鈕 | 用途 |
|----------|------|------|
| 採購報表 | `btnBsPurchaseReport` / `btnReportPurchase` | 採購明細彙總 |
| 供應商採購報表 | `btnReportPurchaseSupplierApparel` | 按供應商查看採購 |
| 採購彙總 | `btnPurchaseReportSum` | 採購總量彙總 |
| PO數量彙總 | `btnPOQtySumReport` | PO數量統計 |
| 銷售報表(按款號) | `btnBsSalesReportApparel` | 按款號維度銷售 |
| 月度銷售報表 | `btnMonthReportSalseApparel` | 月度銷售統計 |
| 客戶銷售報表 | `btnReportCustomerSalseApparel` | 按客戶查看銷售 |
| 發票報表 | `btnBsInvoiceReport` | 發票清單 |
| 應收賬齡 | `btnARsAgingReport` | 應收賬款賬齡分析 |
| 應付賬齡 | `btnAPsAgingReport` | 應付賬款賬齡分析 |

### 8.2 報表操作通用流程

| 步驟 | 操作 |
|------|------|
| 1 | 點擊對應報表按鈕 |
| 2 | 如果彈出篩選條件窗口，設置： |
| | - 日期範圍（起始/截止） |
| | - 客戶/供應商範圍 |
| | - 款號/產品範圍 |
| | - 銷售員/買手 |
| 3 | 點擊 **查詢（Query）** 或 **生成報表** |
| 4 | Crystal Reports 窗口顯示報表 |
| 5 | 使用 Crystal Reports 工具欄： |
| | - **打印（Print）** — 紙質輸出 |
| | - **導出（Export）** — 導出爲 PDF/Excel/Word |
| | - **縮放（Zoom）** — 調整顯示比例 |
| | - **搜索（Search）** — 報表內搜索 |

---

## 9. 系統管理

### 9.1 數據庫連接測試

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 進入系統設置 | |
| 2 | 點擊 **測試數據庫連接（TestDB）** | `btnTestDB` |
| 3 | 成功則顯示連接正常 | |
| 4 | 失敗則檢查網絡和配置 | |

### 9.2 數據導出到Excel

| 步驟 | 操作 |
|------|------|
| 1 | 在任何數據列表界面 |
| 2 | 右鍵點擊 DataGridView |
| 3 | 選擇 **導出到Excel（Export）** |
| 4 | 系統使用 NPOI + Aspose.Cells 生成 Excel 文件 |
| 5 | 選擇保存路徑 |

### 9.3 附件管理

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 在任意單據（PO/SO/Invoice）界面 | |
| 2 | 點擊 **添加附件（AddFile）** | `btnAddFile` |
| 3 | 選擇文件上傳 | |
| 4 | 查看已上傳附件 | `btnBrowseFile` |
| 5 | 刪除附件 | `btnDelFile` |

### 9.4 圖片管理（產品圖片）

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 進入產品維護界面 | |
| 2 | 點擊 **添加圖片（ImgAdd）** | `btnImgAdd` |
| 3 | 選擇圖片文件（支持多張） | |
| 4 | 系統在界面右上角顯示圖片縮略圖（pbItemPic1~4） | |
| 5 | 查看大圖 | `btnImgView` |
| 6 | 刪除圖片 | `btnImgDelete` |

### 9.5 系統更新

| 步驟 | 操作 | 按鈕 |
|------|------|------|
| 1 | 點擊 **檢查更新（UpdatePath）** | `btnUpdatePath` |
| 2 | 系統從配置的 UpdatePath 檢查新版本 | |
| 3 | 如有更新，下載並安裝 | |

---

## 附錄

### A. 常用功能按鈕速查表

| 按鈕 | 功能 | 中文含義 |
|------|------|----------|
| btnAdd | Add New Record | 新增 |
| btnEdit | Edit Record | 編輯 |
| btnSave | Save Record | 保存 |
| btnDelete | Delete Record | 刪除 |
| btnQuery / btnSearch | Search / Query | 查詢 |
| btnCheck | Approve / Check | 審覈 |
| btnUnCheck | Unapprove | 取消審覈 |
| btnRevised | Revise Order | 修訂 |
| btnPrint | Print | 打印 |
| btnImport | Import from Excel | 導入 |
| btnExport / DataGridViewToExcel | Export to Excel | 導出 |
| btnAddRecord | Add Line Item | 添加明細行 |
| btnDelRecord | Delete Line Item | 刪除明細行 |
| btnModifySave | Save Changes | 保存修改 |
| btnBaseModify | Base Data Modify | 基礎數據修改 |
| btnAdminModify | Admin Modify | 管理員修改 |
| btnAdminDelete | Admin Delete | 管理員刪除 |
| btnRefresh | Refresh Data | 刷新 |
| btnCancel | Cancel | 取消 |
| btnClose | Close Form | 關閉 |
| btnEmail | Send Email | 發送郵件 |
| btnPaste | Paste Data | 粘貼 |
| btnMoveUp / btnMoveDown | Reorder Items | 上移/下移 |
| btnProcessAdd / Delete / Up / Down | Process Management | 工序管理 |

### B. 代碼速查

- **配置路徑**：`ITSERP.exe.config`（與 EXE 同目錄）
- **數據庫**：SQL Server `C3_SMD_New_Test`
- **Web Service**：`http://192.168.200.78:3636/Service1.asmx`
- **TCP Port**：13000（自定義協議通信）
- **日誌框架**：log4net
- **Excel 導出**：NPOI / Aspose.Cells
- **報表引擎**：Crystal Reports (RPT)
- **UI 組件庫**：DevComponents DotNetBar2
