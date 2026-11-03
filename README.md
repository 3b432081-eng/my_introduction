# 個人介紹網站（Vue 3 + Vite）

## 執行方式

1. 安裝 Node.js。
2. 解壓縮專案並在終端機切換到 `personal-intro-vue3` 資料夾。
3. 安裝依賴：

   ```bash
   npm install
   ```

4. 啟動開發伺服器：

   ```bash
   npm run dev
   ```

5. 按終端機顯示的網址開啟網頁。

## ProjectCard 元件

`src/components/ProjectCard.vue` 使用 `defineProps()` 接收：
- `title`：作品標題（必填）
- `content`：作品內容（必填）
- `tag`：作品標籤（選填）

作品資料放在 `src/App.vue` 的 `projects` 陣列，透過 `v-for` 產生卡片。

## 個人照片

如果要顯示原本的 `me.jpg`，請將照片放在專案根目錄（與 `package.json` 同一層），Vite 開發時會透過 `/me.jpg` 載入。若沒有照片，可將 `App.vue` 中的 `<img src="/me.jpg" ... />` 改成文字或移除。
