# AXETube デプロイ用パッケージ

`public/index.html` が本体。各プラットフォーム用の設定を同梱しています。

```
deploy/
├── public/index.html      # 公開する静的サイト本体
├── server.js              # Node 静的サーバ (Railway 用)
├── package.json
├── vercel.json            # Vercel
├── render.yaml            # Render (Static Site)
├── railway.json           # Railway (Nixpacks + node server.js)
└── gas/
    ├── Code.gs            # doGet() で Index.html を返す
    ├── Index.html         # GAS 用 HTML (public/index.html と同じ)
    └── appsscript.json
```

## 1. Vercel
```bash
cd deploy
npx vercel --prod
```
`vercel.json` で `public/` を出力ディレクトリに指定済み。SPA リライト有効。

## 2. Render
GitHub に `deploy/` を push し、Render ダッシュボードで **Blueprint → render.yaml** を選択。
タイプは Static Site で、`public/` を配信します。

## 3. Railway
```bash
cd deploy
railway up
```
Nixpacks が `package.json` を検出し、`node server.js` で起動します（`PORT` 環境変数を自動で受信）。

## 4. Google Apps Script (GAS)
### clasp を使う場合
```bash
npm i -g @google/clasp
clasp login
clasp create --type webapp --title AXETube --rootDir ./gas
clasp push
clasp deploy
```
### 手動の場合
1. https://script.google.com で新規プロジェクト作成
2. `Code.gs` の中身をコピペ
3. ファイル > 新規 > HTML で `Index` を作成し、`gas/Index.html` の中身を貼り付け
4. デプロイ > 新しいデプロイ > 種類「ウェブアプリ」
5. 「アクセスできるユーザー: 全員」で公開 → 発行された `/exec` URL を開く

> GAS では `iframe` 内で動く制約があるため、外部リソースのうち一部は読み込めない場合があります。
