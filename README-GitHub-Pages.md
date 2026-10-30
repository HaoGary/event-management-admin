# 後台部署到 GitHub Pages（已建置，直接上傳）

這個資料夾是「可直接服務」的後台網站，**不需要在 GitHub 上建置**，整包上傳即可。
已包含：React 後台（登入頁＋主後台）＋ 預覽頁（preview.html）與其所需的活動頁素材。

## 資料夾結構
```
（repo 根目錄 或 /docs）
├─ index.html          ← 登入頁（首頁；最先開啟、最輕量）
├─ app.html            ← 主後台（登入後進入；網址如 app.html#/theme）
├─ api-config.js       ← 後端網址設定（React 後台用）★要改這個
├─ assets/             ← React 後台程式（分塊 .js/.css）＋ img/（活動頁圖片，給預覽用）
├─ preview.html        ← 後台右側「即時預覽」載入的活動頁
├─ css/                ← 預覽頁樣式（tokens.css、base.css、site/…）
├─ js/                 ← 預覽頁程式（core/、domain/、site/）
│   └─ core/api-config.js  ← 預覽頁用的後端網址（可與上面設成一樣）
└─ .nojekyll           ← 關閉 GitHub 的 Jekyll，避免忽略 assets 內檔案（勿刪）
```

## 一、先改後端網址（重要）
用文字編輯器打開 **`api-config.js`**，確認兩個值：
- `ICHIBAN_API_URL`：你的 Google Apps Script「網頁應用程式」部署網址（結尾是 `…/exec`）。
  目前已填：`…/AKfycbzLr6aE6…/exec`，換部署時記得更新。
- `ICHIBAN_SITE_URL`：**公開活動頁的網址**（因為這包只放後台，活動頁在別處）。
  例如 `https://你的網域/activity/`。發佈成功彈窗的「活動頁網址」會用到；留空的話會誤指到登入頁。

> 若預覽頁也要連後端，`js/core/api-config.js` 內的 `ICHIBAN_API_URL` 設成同一個網址即可
> （預覽主要靠後台用 postMessage 餵資料，通常不改也能預覽）。

Apps Script 需以「**任何人**」可存取的方式部署，GitHub Pages 的網頁才呼叫得到。

## 二、放到 GitHub 並開啟 Pages
兩種常見做法，擇一：

**做法 A：放在 main 分支根目錄**
1. 把本資料夾「裡面的所有檔案」放到你的 repo 根目錄（含 `.nojekyll`）。
2. commit、push 到 `main`。
3. GitHub repo →「Settings」→「Pages」→ Source 選「Deploy from a branch」，
   Branch 選 `main`、資料夾選 `/ (root)`，Save。
4. 等一兩分鐘，網址會是 `https://<帳號>.github.io/<repo>/`（開啟即登入頁）。

**做法 B：放在 /docs（想讓原始碼與網站分開時）**
1. 在 repo 建一個 `docs/` 資料夾，把本資料夾內容放進 `docs/`。
2. push 後，Pages 的資料夾選 `/docs`。

## 三、子路徑沒問題
網站在 `…github.io/<repo>/` 這種子路徑下也能運作：
- 本專案用**相對路徑**與 **HashRouter**，不需改任何 base 設定，也不需額外的 404／轉址設定。
- 登入後會自動進到 `app.html#/…`；未登入直接開 app.html 會自動導回登入頁。

## 四、日後更新後台
在 `admin-react` 專案重新建置，再覆蓋這裡的檔案即可：
```
npm install        # 第一次
npm run build      # 產生 dist/
```
把新的 `dist/index.html`、`dist/app.html`、`dist/api-config.js`、`dist/assets/*`
覆蓋到本資料夾對應位置（`assets/` 用「合併覆蓋」，保留裡面的 `img/`），再 push。
（活動頁若有更新，另把新的 `preview.html`、`css/`、`js/`、`assets/img/` 一起覆蓋。）

## 小提醒
- `.nojekyll` 請保留，否則部分檔案可能被 GitHub 忽略。
- 活動頁預覽會用到 cdnjs 的動畫程式（GSAP），需要能連外網（GitHub Pages 沒問題）。
- repo 設為 Public 時，Pages 免費；Private repo 需付費方案才可用 Pages。
