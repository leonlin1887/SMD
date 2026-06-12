# C3 Enterprise ERP 操作標準作業程序 (SOP)

**系統版本：** C3 - New Ver.2025  
**適用公司：** Shimada Shoji (HK) Limited  
**更新日期：** 2025-04-17  
**模塊範圍：** 銷售出貨全流程（採購訂單 → 入庫 → 出貨單 → 發票 → 裝箱 → 物流安排 → 打包）

---

## 目錄

1. [採購訂單管理 (PO Entry)](#1-採購訂單管理-po-entry)
2. [採購入庫 (PO Inbound)](#2-採購入庫-po-inbound)
3. [出貨單建立 (Delivery Order)](#3-出貨單建立-delivery-order)
4. [商業發票 (Commercial Invoice)](#4-商業發票-commercial-invoice)
5. [裝箱單 (Packing List)](#5-裝箱單-packing-list)
6. [出貨指示 (DO Outbound)](#6-出貨指示-do-outbound)
7. [多 PO 打包 (Pack Multiple PO)](#7-多-po-打包-pack-multiple-po)
8. [常見問題與注意事項](#8-常見問題與注意事項)

---

## 1. 採購訂單管理 (PO Entry)

### Purpose 目的
管理供應商採購訂單，記錄訂購產品的明細、數量、價格及交期。

### Prerequisites 前置條件
- 已登入 C3 Enterprise 系統
- 擁有 Purchase 模塊權限

### Steps 操作步驟

| # | 操作 | 畫面位置 | 說明 |
|:-:|:---|:---------|:-----|
| 1.1 | 進入 PO 管理 | 左側導航 → **Master Data** → **Purchase** → **Order P/O Entry** | 顯示所有 PO 列表 |
| 1.2 | 查詢 PO | 列表上方篩選欄，輸入 PO No./Supplier/Date Range | |
| 1.3 | 新建 PO | 點擊工具欄 **New** 按鈕 | 填寫 Supplier, ItemCode, Qty, Price, Delivery Date |
| 1.4 | 編輯 PO | 選中一行 → **Edit** | 修改數量、價格、交期等 |
| 1.5 | 導出 Excel | **Export** 按鈕 | ⚠️ 導出後若修改了 PO，需重新發送 Excel 給供應商 |

### Key Fields 關鍵欄位說明

| 欄位 | 說明 | 範例 |
|:----|:----|:----|
| PO No. | 採購訂單編號 | PO-2505980 |
| Supplier | 供應商名稱 | Lee Bou International Co., Limited |
| ItemCode | 產品編碼 | 6951-SS |
| ItemName | 產品名稱 | Polyester Tape |
| Size / Color | 規格 / 顏色 | 4mm / 509Black |
| PO Qty | 訂購數量 | 7,800 |
| Unit | 單位 | Meter |
| Unit Price | 單價 | 0.0493 USD |

---

## 2. 採購入庫 (PO Inbound)

### Purpose 目的
記錄供應商送達貨物的實際到貨資訊，核對與 PO 的差異。

### Steps 操作步驟

| # | 操作 | 畫面位置 | 說明 |
|:-:|:---|:---------|:-----|
| 2.1 | 切換到 PO Inbound | 左側導航 → **Base Data** → **In Process** → **PO Inbound** | |
| 2.2 | 導入 PO 數據 | 點擊 **Import POList** 或拖入之前導出的 Excel | 也可點擊 **Import Export** 批量導入 |
| 2.3 | 填寫入庫資訊 | 表頭區域： | |
| | Receiving Date | 選擇收貨日期 | 2025/04/17 |
| | Warehouse | 選擇倉庫 | SMD Warehouse |
| | Zone | 選擇儲存區域 | A1, A2 |
| 2.4 | 核對 Item 明細 | 下方表格逐行核對： | |
| | Rec Qty | 實際收貨數量 | 與 PO Qty 核對 |
| | Balance | 未到貨數量（= PO Qty - Rec Qty） | |
| | Zone | 儲位分配 | |
| 2.5 | 確認入庫 | 點擊 **Check** → 狀態變為已 Check | |

---

## 3. 出貨單建立 (Delivery Order)

### Purpose 目的
根據客戶訂單建立出貨單，記錄發貨明細、收貨方資訊、交貨地址等。

### Prerequisites 前置條件
- 客戶訂單已確認
- 庫存充足

### Steps 操作步驟

| # | 操作 | 畫面位置 | 說明 |
|:-:|:---|:---------|:-----|
| 3.1 | 進入 Delivery Order | 左側導航 → **Sales** → **Delivery Order List** | |
| 3.2 | 新建 DO | 點擊 **New** | |
| 3.3 | 填寫 Header 資訊 | | |
| | Customer Name | 選擇客戶 | Crystal Sweater Limited |
| | Consigned To | 收貨方 | 通常與 Customer 相同 |
| | D.Address | 送貨地址 | 5-7/F, AXA Tower... Kwun Tong, Kowloon, HK |
| | To Country | 目的地國家 | Slovenia |
| | Payment | 付款條件 | AMS 30 days |
| | Currency | 貨幣 | USD |
| | Warehouse | 出貨倉庫 | UNIQLO / SMD Warehouse |
| | Salesman | 業務員 | Kevin Tsoi |
| | Manager | 經理 | Kevin Tsoi |
| 3.4 | 填寫 Item 明細 | 在表格中新增行： | |
| | ItemCode | 產品編碼 | 6951-SS |
| | ItemName | 產品名稱 | Polyester Tape |
| | Size / Color | 規格 / 顏色 | 4mm / 509Black |
| | Quantity | 出貨數量 | 7,800 |
| | Unit | 單位 | Meter |
| | Price | 單價 | 0.0493 |
| | Warehouse | 倉庫 | SMD Warehouse |
| | Zone | 儲位 | A1 |
| 3.5 | 確認 | 點擊 **Check** → 核對總數量、金額 | |
| 3.6 | 發送 | 點擊 **Sent** → DO 狀態變為已發送 | ⚠️ Sent 後無法直接編輯（見注意事項） |

### 常見 DO 明細示例

| ItemCode | ItemName | Size | Color | Qty | Unit | Price USD | Amount |
|:---------|:---------|:----:|:-----:|:---:|:----:|:---------:|:------:|
| 6951-SS | Polyester Tape | 4mm | 509Black | 7,800 | Meter | 0.0493 | 384.54 |
| 6951-SS | Polyester Tape | 4mm | 01 Off White | 3,000 | Meter | 0.0493 | 147.90 |
| 6951-SS | Polyester Tape | 4mm | 39 Brown | 6,000 | Meter | 0.0493 | 295.80 |
| **Total** | | | | **27,600** | Meter | | **1,360.68** |

### Header 資訊參考
- **DO No.:** DO-29040558
- **DO Date:** 2025/04/17
- **PackingList NO:** PL-2502630
- **Export InvoiceNO:** A-25040366

---

## 4. 商業發票 (Commercial Invoice)

### Purpose 目的
開立正式商業發票給客戶，用於報關和收款。

### Steps 操作步驟

| # | 操作 | 畫面位置 | 說明 |
|:-:|:---|:---------|:-----|
| 4.1 | 進入 Invoice | 從 DO 頁面點擊 **Invoice** 按鈕 | 系統自動帶入 DO 數據 |
| 4.2 | 確認發票資訊 | | |
| | Invoice No. | 系統自動生成 | A-25040366 |
| | Invoice Date | 系統自動填入 | 2025/04/17 |
| | Customer | 自動帶入 DO 的客戶 | Crystal Sweater Limited |
| | Consignee | 收貨人 | 與 DO 一致 |
| 4.3 | 核對 Item 明細 | 確認數量、單價、金額正確 | |
| 4.4 | 確認 Summary | | |
| | SumQty | 總數量 | 27,600.00 |
| | SubItemAmount | 貨物金額 | 1,360.68 USD |
| 4.5 | 保存並發送 | 點擊 **Save** → 可導出 PDF/Excel | |

### Invoice 結構
```
發票抬頭：Shimada Shoji (HK) Limited
Invoice No.: A-25040366
Date: 2025/04/17
客戶: Crystal Sweater Limited
目的地: Slovenia (via Hong Kong)
付款條件: AMS 30 days
```

> 💡 **提示：** 可在 Invoice 頁面生成 **Temp Invoice**（臨時發票號），用於先發送給客戶確認後再正式開立。

---

## 5. 裝箱單 (Packing List)

### Purpose 目的
記錄每箱貨物的包裝明細（箱號、品項、數量、重量、體積），用於物流和報關。

### Steps 操作步驟

| # | 操作 | 畫面位置 | 說明 |
|:-:|:---|:---------|:-----|
| 5.1 | 進入 Packing List | 從 DO 頁面點擊 **Packing List** 按鈕 | 或在 Invoice 頁面點擊 |
| 5.2 | 確認 Packing List 資訊 | | |
| | PL No. | 系統自動生成 | PL-2502630 |
| | Date | 2025/04/17 |
| | Invoice Ref | A-25040366 |
| 5.3 | 核對 Item 明細 | 每行包含：BoxCode, ItemCode, Color, Qty, NW, GW, CBM | |
| 5.4 | 確認裝箱匯總 | | |
| | SumCtns | 總箱數 | 1 |
| | SumQty | 總數量 | 27,600.00 |
| | SumNW | 總淨重 | 5.05 kg |
| | SumGW | 總毛重 | 5.68 kg |
| | SumCBM | 總體積 | 0.0276 m³ |
| 5.5 | 報表生成 | 系統調用 SAP Crystal Reports 生成正式 Packing List PDF | 顯示「文件處理中，請稍候」 |

### Packing List 欄位說明

| 欄位 | 說明 | 範例 |
|:----|:----|:----|
| Customer PO | 客戶 PO 號 | TEST 1 |
| PI NO | PI 編號 | PI-3393810001~0006 |
| BoxCode | 箱號 | 1 |
| Customer Parts | 客戶料號 | 6951-SS |
| Item Name | 產品名稱 | Polyester Tape |
| Size | 規格 | 4mm |
| Color | 顏色 | 001 Off White |
| Qty | 數量 | 7,800 |
| Unit | 單位 | Meter |
| N.W.(kg) | 淨重 | 5.05 |
| G.W.(kg) | 毛重 | 5.68 |
| (M³) | 體積 | 0.0276 |

---

## 6. 出貨指示 (DO Outbound)

### Purpose 目的
安排實際物流出貨，指定物流公司、預計發貨/到達時間、運單號等。

### Steps 操作步驟

| # | 操作 | 畫面位置 | 說明 |
|:-:|:---|:---------|:-----|
| 6.1 | 進入 Outbound | 左側導航 → **Out Process** → **DO Outbound(Instruction)** | |
| 6.2 | 選擇 DO | 在列表中選擇要安排物流的 DO | DO-EB040558 |
| 6.3 | 填寫物流資訊 | | |
| | Shipping Type | 運輸方式 | 支持貨運 / Courier |
| | Logistics Company | 物流公司 | L0002 綠記貨車 |
| | AWB/SO NO | 運單號 | TEST |
| | ETD | 預計發貨日 | 2025/04/18 |
| | ETA | 預計到達日 | |
| | Cut off time | 截止時間 | 13:00 |
| | Status by Logistics Dept | 物流部門狀態 | Move to A2 |
| 6.4 | 保存 | 點擊 **Save Delivery Info** | |
| 6.5 | 確認發送 | 點擊 **Sent** → **GO** | |

### ⚠️ 重要規則
> **編輯已發送的 DO：** 如果 DO 已 Sent，需要 Logistics 部門先 Cancel，將其標記為已取消後才能修改。
> **直運訂單：** For direct shipments, delete the DO and recreate it.

---

## 7. 多 PO 打包 (Pack Multiple PO)

### Purpose 目的
將多個採購訂單的貨物合併打包，管理箱號、儲位和包裹資訊。

### Steps 操作步驟

| # | 操作 | 畫面位置 | 說明 |
|:-:|:---|:---------|:-----|
| 7.1 | 進入 Pack Multiple PO | 左側導航 → **Stock** → **Pack Multiple PO** | |
| 7.2 | 選擇訂單 | 在上方表格勾選要打包的 PO | |
| 7.3 | 分配箱號 | 在下方 Item 表格指定 BoxNO | |
| 7.4 | 填寫包裹資訊 | Long, Wide, Height, CBM, NW, GW | |
| 7.5 | 合併箱資訊 | 選中同一 Carton 的多行 → 右鍵 → **Merge CtnInfo** | 合併相同 Carton 的明細 |
| 7.6 | 填寫 Memo | 備註欄填寫特殊指示 | 「請務必將箱放入一箱」|

### Pack Multiple PO 表格欄位

**上層（Order Details）：**
ID | TransDate | OrderNO | Status | Warehouse | Zone | BoxNO | CBM | NW | Gross | Memo | CreateUser/Date | CheckUser/Date

**下層（Item Details）：**
SubPANO | SubPONO | Supplier Code | Salesman | Apparel | Supplier Name | Warehouse | Zone | Qty | ItemCode | ItemName | Color | Size | BoxNO

---

## 8. 常見問題與注意事項

### ❓ Q&A

**Q1: DO 已 Sent 後發現錯誤怎麼辦？**
> A: 請聯繫 Logistics 部門 Cancel 該 DO，狀態變為 Canceled 後才能編輯，或直接刪除重建。

**Q2: Excel 導出後修改了 PO 需要做什麼？**
> A: 必須重新發送 Excel 給供應商。導出時的提示：「Export Excel to supplier, please resend this excel if you edited the PO」

**Q3: Packing List 中重量/體積為 0 正常嗎？**
> A: 部分 Item 的 NW/GW/CBM 可能為 0，需要在 Pack Multiple PO 時手動填寫。確保在打包階段完整填寫包裹尺寸重量。

**Q4: 同一個 DO 出現在不同模塊中如何管理？**
> A: DO 在 Sales → Delivery Order 模塊建立和 Check，在 Out Process → DO Outbound 安排物流。兩個模塊管理不同階段的資訊，並非重複操作。

### ⚡ 快捷鍵/技巧

| 操作 | 方式 |
|:----|:-----|
| 刷新數據 | 點擊 **Refresh Cache** 按鈕 |
| 打印當前畫面 | **PrintScreen** 按鈕 |
| 鎖定系統 | **Lock** 按鈕（離開座位時使用） |

### 📋 流程檢查清單

- [ ] PO 已建立並導出 Excel 給供應商
- [ ] 供應商發貨 → PO Inbound 已錄入
- [ ] DO 已建立，Item 明細和數量正確
- [ ] DO 已 Check + Sent
- [ ] Invoice 已開立（編號 A-xxxxxx）
- [ ] Packing List 已生成（編號 PL-xxxxxx）
- [ ] 物流資訊已填寫（ETD/ETA/物流公司）
- [ ] Outbound 已 Sent
- [ ] 打包完成（Pack Multiple PO）

---

*本 SOP 基於 C3 Enterprise ERP 操作錄影分析生成（2025-04-17）*
*Document generated from C3 Enterprise ERP operation video analysis*
