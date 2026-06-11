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

   - **SCP 指令（建議）**：

     **基本語法**
     ```
     scp [選項] <來源檔案> <使用者名稱>@<VM_IP>:<目標路徑>
     ```

     **常用選項**

     | 選項 | 說明 |
     |------|------|
     | `-P <port>` | 指定 SSH 連接埠（預設為 22，注意為大寫 P） |
     | `-i <金鑰路徑>` | 使用指定的 SSH 私鑰進行驗證（Key-based auth） |
     | `-r` | 遞迴複製整個資料夾 |
     | `-v` | 顯示詳細傳輸過程（用於除錯） |

     **範例指令**
     ```bash
     # 基本用法（預設 Port 22，密碼驗證）
     scp photo.jpg username@192.168.1.100:/var/www/html/images/

     # 指定自訂連接埠（例如 Port 2222）
     scp -P 2222 photo.jpg username@192.168.1.100:/var/www/html/images/

     # 使用 SSH 私鑰驗證
     scp -i ~/.ssh/id_rsa photo.jpg username@192.168.1.100:/var/www/html/images/

     # 遞迴上傳整個資料夾
     scp -r my_website/ username@192.168.1.100:/var/www/html/
     ```

     > ⚠️ **注意**：`scp` 預設使用 SSH Port **22**。若 VM 的 SSH 服務監聽於其他埠號，必須加上 `-P` 指定。

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

   **基本語法**
