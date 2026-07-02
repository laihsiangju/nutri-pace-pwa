# Nutri Pace PWA

這是一個可部署到 private GitHub repo 的 PWA 版本。程式碼可以保持 private，使用者資料預設存在瀏覽器本機，填入 Google OAuth Client ID 後可同步到你的私人 Google Drive app data JSON。

## 檔案

- `index.html`：App 介面與本機資料邏輯
- `app-config.js`：Google OAuth Client ID 與 Drive JSON 檔名
- `manifest.webmanifest`：PWA 安裝設定
- `sw.js`：離線快取
- `icon.svg`：App 圖示

## 目前功能

- 每日飲食紀錄、熱量與蛋白質目標追蹤
- 主畫面只保留每日食物輸入、目前攝取、智能晚餐建議；其他設定與報表由左上角主選單進入單獨頁面
- 食物清單已合併，不分早午晚餐；先選餐別後，每一餐都可使用完整食物選單
- 常用餐組合會固定顯示在新增餐點選項中，可一鍵加入目前選定餐別
- 食物資料庫可在 App 內管理完整清單：新增、原列編輯、刪除個人食物，或覆蓋/還原預設食物
- 已新增完成的食物在事後編輯時，會直接在原本那一列修改，不會跳回上方新增區
- 新增食物時若熱量或蛋白質空白或為 0，系統會用同名、相近食物、餐點類型或重量標記自動估算，例如 `地瓜200g`
- 熱量與蛋白質目標可用快捷按鈕或自訂數值調整
- 智能晚餐建議會依照今日剩餘熱量、蛋白質缺口，從目前食物資料庫包含個人新增食物中動態產生 1 到 3 項晚餐組合
- 智能晚餐內另有「清單外合理組合」次要推薦，使用預設熱量與蛋白質估算；套用後會存入個人食物資料庫並加入晚餐
- 「已存入食物」與「清單外合理組合」各自有重新排列按鈕，可刷新合理範圍內的不同推薦組合
- 生理週期飲食模式
- 每日身體紀錄：日期、體重、體脂、皮下脂肪、內臟脂肪
- 週報表 / 月報表：體重、體脂率、脂肪量、瘦體重變化
- 首頁可直接快速更新今日體重與體脂
- BMR 由系統依體重自動估算，不需手動輸入
- 不內建個人身高、體重、體脂或飲食紀錄；首次使用會從空白狀態開始
- Google Drive JSON 同步與本機備份

## Google Drive 同步設定

1. 到 Google Cloud Console 建立一個 Project。
2. 啟用 Google Drive API。
3. 建立 OAuth Client ID，類型選 Web application。
4. Authorized JavaScript origins 加入你的部署網址，例如 `https://你的帳號.github.io` 或你選用的 private 部署網域。
5. 把 Client ID 貼到 `app-config.js`：

```js
window.NUTRI_PACE_CONFIG = {
  googleClientId: "你的-client-id.apps.googleusercontent.com",
  driveFileName: "nutri-pace-data.json"
};
```

## 資料儲存方式

- 未登入 Google：資料存在目前裝置的瀏覽器本機儲存。
- 登入 Google 後：App 會讀寫 Google Drive 的 `appDataFolder` 裡的 `nutri-pace-data.json`。
- 前端不需要、也不應該放任何 Google secret 或 service account key。
- 身體組成報表中的脂肪量以 `體重 × 體脂率` 推估，瘦體重以 `體重 - 脂肪量` 推估。
- 食物資料庫異動會存在同一份 state：`personalFoods` 保存新增/編輯，`hiddenFoods` 保存被隱藏的預設食物；登入後會一起同步到 Drive。

## 部署建議

最符合目前方向的配置是：

- GitHub repo 設成 private，只保存程式碼。
- 部署到支援登入保護或私人瀏覽的靜態網站服務。
- 手機打開部署網址後，使用瀏覽器的「加入主畫面」安裝成 PWA。

如果使用一般公開 GitHub Pages，程式碼 repo 可以 private，但網站本身不一定是 private。要做只有你看得到的網站，請搭配有 access control 的部署方式。
