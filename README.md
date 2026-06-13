# FlexiMeter - 數位個人化計程車計費器 🚖

FlexiMeter 是一款為獨立駕駛、共享接送及點對點運輸情境量身打造的**輕量級、零外部相依、高動態精準度數位計費軟體**。

本專案完全基於 **PWA (Progressive Web App)** 架構開發，採用單一 HTML 檔案實作，並具備完整的離線運行能力。

👉 **線上展示網址：[https://rackliu.github.io/FlexiMeter/](https://rackliu.github.io/FlexiMeter/)**

---

## 🌟 核心特色

- **單網頁架構 (Single-File)**：所有 HTML 結構、Tailwind CSS v4 樣式、JavaScript 計費邏輯與地圖繪製皆包裝在單一 `index.html` 中，無任何 npm/node 依賴。
- **雙軌制計費引擎 (Dual-Tariff Billing Engine)**：
  - 當車速 $\ge 10\text{ km/h}$ 時：累積里程，不計算時間費。
  - 當車速 $< 10\text{ km/h}$ (包含怠速/停等紅燈) 時：暫停累積里程計費，直接累積等候時間。
  - 起步里程（起跳區間，預設 1250m）內不另行加收里程費與等候費。
- **PWA 離線支援**：內嵌 Blob-based Service Worker，自動快取地圖套件與樣式表。即使開車至收訊不良的山區或隧道，App 依然能流暢啟動與離線計費。
- **AMOLED 純黑高對比設計**：專為高壓力駕駛環境設計，大字體、高對比的車資看板、超大點擊區域，並支援 **Screen Wake Lock** 防止手機螢幕自動休眠。
- **安全防誤觸結束**：結束行程按鈕需要**持續長按 2 秒**並顯示進度條，防止駕駛在顛簸路段或行車操作時意外中斷計費。
- **路徑模擬功能 (測試專用)**：內建 GPS 軌跡與車速模擬器。免出門、免 GPS 訊號，即可在電腦瀏覽器上直接展示跳表、等紅燈、加速、結算的完整生命週期。
- **司機付款二維碼**：支援設定司機個人付款連結（如 Line Pay、街口、銀行轉帳），結算時自動生成付款 QR Code，便於乘客掃描。
- **語音播報 (TTS)**：行程開始與結束時自動進行繁體中文語音播報。

---

## 📐 計費數學模型與參數

### 預設計費標準 (可至系統設定修改)
- **起步價 (日間)**：NT$ 85
- **起步里程**：1,250 公尺
- **續程跳表里程**：每 200 公尺
- **續程加收費率**：NT$ 5
- **低速等候門檻**：時速低於 10 km/h (2.78 m/s)
- **低速等候時間**：每 60 秒
- **等候加收費率**：NT$ 5
- **夜間加成起步價**：NT$ 105

### 雙軌制公式
1. **車速判斷**：
   $$\Delta d = \text{Haversine}(\text{Point}_{n-1}, \text{Point}_n)$$
   $$\Delta t = t_n - t_{n-1}$$
   $$v = \frac{\Delta d}{\Delta t} \times 3.6 \text{ (km/h)}$$
2. **累加規則**：
   - 若 $v \ge 10\text{ km/h}$，則 $D_{\text{total}} \leftarrow D_{\text{total}} + \Delta d$。
   - 若 $v < 10\text{ km/h}$，則 $T_{\text{idle}} \leftarrow T_{\text{idle}} + 1\text{ 秒}$。
3. **車資計算 ($D_{\text{total}} > 1250\text{m}$)**：
   $$\text{車資} = \text{起步價} + \left\lfloor \frac{D_{\text{total}} - 1250}{\text{續程距離 (200m)}} \right\rfloor \times \text{續程費率 (5)} + \left\lfloor \frac{T_{\text{idle}}}{\text{等候時間 (60s)}} \right\rfloor \times \text{等候費率 (5)}$$

---

## 📲 PWA 安裝指南

由於本軟體符合 Progressive Web App 規範並提供 HTTPS：

### iOS (Safari)
1. 使用 Safari 開啟 [FlexiMeter 網址](https://rackliu.github.io/FlexiMeter/)。
2. 點擊瀏覽器底部的**分享**按鈕（向上箭頭方塊）。
3. 選擇 **加入主畫面 (Add to Home Screen)**。

### Android (Chrome)
1. 使用 Chrome 開啟網頁。
2. 點擊頂端或選單中的 **安裝應用程式 (Install App)** 提示。
3. 確認後，App 即會出現在您的手機桌面上。

---

## 🛠️ 本地開發與運行

本專案沒有任何編譯步驟，可以直接使用任何簡易的 HTTP 伺服器啟動：

```bash
# 使用 Python 啟動本地伺服器
python -m http.server 8000
```
瀏覽 `http://localhost:8000` 即可進行調試。

> 💡 **提示**：由於瀏覽器的安全機制，GPS 定位與 Service Worker 需要安全連線（`localhost` 或 `https://`）才能正常啟用。

---

## 📄 授權條款
本專案採用 MIT 授權條款開放開源使用。
