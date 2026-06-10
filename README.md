# 帳號管理系統

Vue 3 + TypeScript 前端面試作品，實作帳號的 CRUD 管理介面。

## 技術堆疊

- Vue 3 + TypeScript
- Vite
- Tailwind CSS v3
- Pinia（狀態管理）
- Vue Router 4
- Axios（含 interceptor）

## 環境需求

- Node.js 18+
- npm 9+

## 啟動專案

```bash
# 安裝套件
npm install

# 啟動開發伺服器
npm run dev
```

開啟瀏覽器至 http://localhost:5173

## 環境變數

複製 `.env` 並填入 API 網址：

```
VITE_API_BASE_URL=https://api-frontend-interview-server.metcfire.com.tw
```

## 功能說明

- **登入**：輸入任意 Email 格式帳號與密碼即可登入，具備表單驗證與 loading 狀態
- **帳號列表**：顯示所有帳號，含總數、啟用中、已停用統計
- **搜尋**：debounce 300ms 前端即時篩選（姓名、Email、角色）
- **新增帳號**：填寫姓名、Email、角色、狀態
- **編輯帳號**：修改現有帳號資料
- **刪除帳號**：含確認提示
- **RWD**：支援手機、平板、桌機版面
- **路由守衛**：未登入自動導向登入頁，已登入無法返回登入頁

## 專案結構

```
src/
├── api/          # axios 封裝與 API 呼叫
├── components/   # AccountCard 元件
├── router/       # Vue Router 路由設定與守衛
├── stores/       # Pinia stores（auth、accounts）
└── views/        # LoginView、DashBoard
```