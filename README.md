# 雲端運算 Level 1 實驗室 (Cloud Computing Level 1 Lab)

![Penguin](https://img.shields.io/badge/Status-Complete-brightgreen)

此實驗室演示了從**本地端（On-premises）** 逐步遷移到 **雲端環境（Cloud Server）** 的完整過程。

## 📋 實驗目標

本實驗通過三個階段展示網站遷移到雲端的過程：
- **階段零**：建立本地網站
- **階段一**：數據遷移 (資料上雲，應用程式保留在地端)
- **階段二**：應用程式遷移 (完全遷移到雲端)

## 🛠️ 實作環境準備

### 系統需求
- **本地端電腦**：模擬傳統「地端伺服器 (On-premises)」
- **Ubuntu 虛擬機 (VM)**：模擬「雲端伺服器 (Cloud Server)」
- **網路要求**：確保本地端電腦與 Ubuntu VM 處於同一區域網路，或本地端可以 ping 通 VM 的 IP 位址

## 📝 步驟零：在地端建立原始網站 (On-premises Setup)

### 目標
在本地電腦建立一個基本的靜態網站

### 步驟

1. **建立資料夾**
   ```bash
   mkdir my_website
   cd my_website
   ```

2. **準備圖片**
   - 將一張圖片命名為 `photo.jpg` 並放入資料夾中

3. **建立 HTML 檔案**
   - 建立 `index.html` 檔案，輸入以下程式碼：
   ```html
   <html>
   <body>
       <h1>我的網站 (目前在地端)</h1>
       <img src="photo.jpg" alt="測試圖片">
   </body>
   </html>
   ```

4. **測試**
   - 在本地端瀏覽器直接點擊開啟 `index.html`
   - 確認文字與圖片皆可正常顯示

---

## 🌐 步驟一：設定模擬雲端環境 (Cloud Server Setup)

### 目標
在 Ubuntu VM 上設定 Apache 網頁伺服器

### 步驟

1. **啟動 Ubuntu VM**
   - 開啟虛擬機並啟動終端機

2. **安裝 Apache 網頁伺服器**
   ```bash
   sudo apt update
   sudo apt install apache2 -y
   ```

3. **查詢 VM 的 IP 位址**
   ```bash
   ip a
   # 或
   hostname -I
   ```
   記錄 IP 位址（例如：`192.168.1.100`）

4. **更改網頁根目錄權限**
   ```bash
   sudo chown -R $USER:$USER /var/www/html
   ```

---

## 📤 步驟二：階段一 - 數據遷移 (Data Migration)

### 目標
將「資料（圖片）」遷移至雲端，「應用程式（HTML）」保留在地端

### 步驟

1. **在 VM 建立圖片資料夾**
   ```bash
   mkdir /var/www/html/images
   ```

2. **上傳圖片**
   - 使用以下任一方法將 `photo.jpg` 傳輸至 VM 的 `/var/www/html/images/` 目錄：
     - **SCP 指令**：
       ```bash
       scp photo.jpg username@192.168.1.100:/var/www/html/images/
       ```
     - **FTP 軟體**（如 FileZilla）
     - **虛擬機拖曳功能**

3. **驗證數據上雲**
   - 在本地端瀏覽器輸入：`http://192.168.1.100/images/photo.jpg`
   - 確認能在網路上讀取到該圖片

4. **修改地端應用程式**
   - 開啟本地端的 `index.html`，將圖片路徑改為雲端絕對路徑：
   ```html
   <html>
   <body>
       <h1>我的網站 (網頁在地端，圖片在雲端)</h1>
       <img src="http://192.168.1.100/images/photo.jpg" alt="測試圖片">
   </body>
   </html>
   ```

5. **階段一測試**
   - 在本地端瀏覽器重新整理 `index.html`
   - 網頁檔案由本機讀取，圖片數據從 Ubuntu VM 載入
   - ✅ 完成混合式架構的漸進遷移

---

## ☁️ 步驟三：階段二 - 應用程式遷移 (Application Migration)

### 目標
將「應用程式（HTML）」也遷移至雲端，完成 100% 雲端化

### 步驟

1. **修改本地端 index.html**
   ```html
   <html>
   <body>
       <h1>我的網站 (完全在雲端運行)</h1>
       <img src="images/photo.jpg" alt="測試圖片">
   </body>
   </html>
   ```
   ⚠️ 注意：HTML 和圖片放在同一台伺服器上，路徑改回相對路徑

2. **上傳 HTML 檔案到雲端**
   ```bash
   scp index.html username@192.168.1.100:/var/www/html/
   ```
   或使用 FTP 軟體上傳（將覆蓋 Apache 原本預設的 index 檔案）

3. **階段二測試**
   - 關閉本地端的 `index.html` 檔案
   - 在本地端瀏覽器網址列輸入：`http://192.168.1.100/`
   - ✅ 確認網頁文字與圖片皆成功顯示

4. **最終確認**
   - 網站數據與應用程式已完全遷移至 Ubuntu VM（雲端環境）
   - 本地端電腦的原始資料夾可刪除

---

## 📁 檔案結構

```
CloudComputing-level1-lab/
├── README.md              # 本文件
├── stage-0/
│   ├── index.html         # 階段零：本地網站
│   └── photo.jpg          # 測試圖片
├── stage-1/
│   └── index.html         # 階段一：修改圖片路徑指向雲端
└── stage-2/
    └── index.html         # 階段二：完全雲端化
```

---

## 🔑 關鍵概念

| 概念 | 說明 |
|------|------|
| **On-premises** | 在本地端伺服器上運行應用程式和存儲資料 |
| **Cloud Server** | 在遠端雲端伺服器上運行應用程式和存儲資料 |
| **混合架構** | 部分資源在本地端，部分在雲端 |
| **數據遷移** | 將資料從本地轉移到雲端 |
| **應用程式遷移** | 將應用程式從本地轉移到雲端 |

---

## 💡 學習要點

✅ 理解本地端 vs 雲端的差異  
✅ 掌握漸進式遷移策略  
✅ 學習混合架構的設計  
✅ 熟悉 Apache 網頁伺服器的基本操作  
✅ 練習檔案傳輸技能（SCP、FTP 等）  

---

## 🎯 常見問題 (FAQ)

**Q: 如何知道 VM 的 IP 位址？**  
A: 在 VM 終端機執行 `ip a` 或 `hostname -I`

**Q: 無法 ping 通 VM，怎麼辦？**  
A: 檢查虛擬機設定，確保網路設定為「橋接模式 (Bridged)」或「NAT」

**Q: 如何使用 SCP 上傳檔案？**  
A: 在本地端終端機執行：`scp 檔案名 username@VM_IP:/目標路徑/`

**Q: 階段一和階段二的差異是什麼？**  
A: 階段一只遷移資料，應用程式仍在本地；階段二將應用程式也遷移到雲端

---

## 📚 相關資源

- [Apache HTTP Server](https://httpd.apache.org/)
- [Ubuntu 伺服器文件](https://ubuntu.com/server)
- [SCP 指令教學](https://linux.die.net/man/1/scp)

---

## 📄 授權

此項目用於教育目的。

**建立日期**: 2026年  
**實驗等級**: Level 1 (入門)
