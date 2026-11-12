# 福岡 7 天 6 夜｜行程＋購物清單

單一 HTML 檔的旅遊行程與購物清單網頁（手機優先），3 人，2026/11/19（四）– 11/25（三），住 Hotel NOVIA FUKUOKA（中央區春吉）。

## 工作規則

- 一律用**中文**回覆
- 需求不清楚時**先提問確認，不要猜測**
- git 只做**本地提交**，**不要 push**
- 新增或修改 UI 時，**風格與現有設計保持一致**（沿用既有 class、CSS 變數、字體、圓角、間距）

## 檔案結構

只有一個檔案：`fukuoka-trip.html`（約 900KB，UTF-8），沒有建置流程、沒有外部 JS，直接用瀏覽器開啟即可。

> ⚠️ 檔案內有多行 base64 圖片，單行可長達 10–24 萬字元（行程頁的 `<img class="it-scene it-photo">`，以及 `<script>` 內的 `const IMG = {...}`）。
> 讀檔時請用行號範圍或 grep 避開這些行，不要整檔讀取；修改時用精確字串替換，不要動到 base64 內容。

| 區塊 | 大約行號 | 說明 |
|---|---|---|
| `<style>` | 10–159 | 全部 CSS（含深色模式） |
| `#view-it` 行程頁 | 172–292 | 7 個 `<section class="it-day">`，靜態 HTML |
| `#view-shop` 購物頁 | 294–317 | 日期切換列、進度條、`<main id="list">`（由 JS 渲染） |
| `#overlay` | 320–327 | 放大照片／型號給店員看的全螢幕視窗 |
| `<script>` | 329–480 | `IMG`、`ITEMS`、`PLAN` 資料 + 渲染邏輯 |

## 設計系統

**CSS 變數**（`:root`，深色模式在 `@media (prefers-color-scheme: dark)` 及 `:root[data-theme="dark"]` 兩處都要同步修改）：

| 變數 | 用途 |
|---|---|
| `--bg` / `--surface` / `--ink` / `--muted` / `--line` / `--tile` / `--done` | 背景、卡片、文字、次要文字、框線、圖塊、已完成 |
| `--indigo` / `--indigo-soft` | 主色（抵達／返台日、按鈕選取狀態、型號標籤） |
| `--mentai` / `--mentai-soft` | 明太子紅（購物日、勾選、強調標籤、訂位提示） |
| `--drive` / `--drive-soft` | 綠色（自駕日） |

**字體**：標題／日期用 `Zen Maru Gothic`（700/900）、內文 `Noto Sans TC`、數字與型號 `Inter`（Google Fonts 載入）。

**常用元件樣式**：卡片 `border:1px solid var(--line); border-radius:16–18px; background:var(--surface)`；膠囊按鈕 `border-radius:999px`；小標籤 `border-radius:6px; font-size:12px; font-weight:700`。版面最大寬 640px（`.wrap`），窄螢幕斷點 420px。

## 行程頁（`#view-it`）

每天一個 section：

```html
<section class="it-day [it-shop|it-drive]">   <!-- 無修飾＝抵達/返台（indigo） -->
  <div class="it-date"><span class="it-md">11/20</span><span class="it-dow">週五</span><span class="it-n">DAY 2</span></div>
  <div class="it-main">
    <img class="it-scene it-photo" ...> 或 <svg class="it-scene" ...>   <!-- 頂部插圖 -->
    <div class="it-theme">標題</div>
    <div class="it-tags"><span class="it-tag [it-shop|it-drive]">…</span></div>
    <ul class="it-route"><li><span class="it-ic" aria-hidden="true">emoji</span>地點</li>…</ul>
    <dl class="it-meals">
      <dt>晚餐</dt><dd>店名<span class="it-bk">要訂 21:30</span><div class="it-alt">備案</div></dd>
    </dl>
    <div class="it-note">備註</div>
    <button class="it-go" data-go="d1120">看這天的購物清單 →</button>   <!-- 有購物清單的日子才有 -->
  </div>
</section>
```

## 購物頁資料（`<script>`）

- `IMG`：`{ key: "data:image/jpeg;base64,..." }` 商品照片
- `ITEMS`：商品定義
  - `img`（對應 IMG key）或 `icon`（emoji）
  - `brand`、`name`、`note`、`codes`（型號陣列，可點擊放大）
  - `browse:true` → 顯示「逛逛」標籤（現場看款，不是指定商品）
- `PLAN`：`[{ id:"d1120", date, dow, theme, stops:[{ place, area, items:[{ id, where }] }] }]`
  - 同一商品可出現在多天（例如「沒買到的話 11/24 再找」），勾選狀態以商品 id 共用
  - 新增日期時要同步：`PLAN`、`DAYS` 陣列、`.days` 按鈕列（目前 4 欄 grid）、行程頁的 `it-go` 按鈕
- 勾選狀態存在 `localStorage`，key：`fukuoka-shopping-v1`
- 網址 hash：`#shop`、`#d1120` 等可直接開到購物頁指定日期

## 驗證

改完用瀏覽器直接開 `fukuoka-trip.html` 檢查：手機寬度（約 375px）、淺色／深色模式、行程與購物頁切換、勾選與放大視窗。
