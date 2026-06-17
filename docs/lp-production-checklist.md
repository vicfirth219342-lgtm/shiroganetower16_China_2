# 不動産LP制作チェックリスト

新しい物件LPを制作・公開するたびにこのリストを使用すること。

---

## Phase 1：制作前準備

- [ ] `templates/property-lp-brief.md` を複製して物件情報を記入
- [ ] 画像素材のカテゴリ分け（現状写真 / インテリア配置写真 / 共用部 / 周辺写真）
- [ ] 動画素材の確認（ヒーロー用MP4 × 2〜4本、YouTubeルームツアーID）
- [ ] Formspreeで新フォームを作成してIDをメモ
- [ ] GitHubに新リポジトリを作成
- [ ] 物件コピー（キャッチコピー、各セクション文言）を準備

---

## Phase 2：HTML制作

### ヘッド・メタ情報
- [ ] `<title>` を物件に合わせて変更
- [ ] `<meta name="description">` を更新
- [ ] OGP（og:title / og:description / og:image）を更新
- [ ] Vercel Analytics スクリプトが入っているか確認（`/_vercel/insights/script.js`）
- [ ] Google Analytics ID `G-XXXXXXXXXX` を実際のIDに変更（または削除）

### ヒーローセクション
- [ ] 動画ファイルパスを新物件用に更新
- [ ] poster画像のパスを更新
- [ ] キャッチコピーを変更（`word-break: keep-all` 適用済みか確認）
- [ ] 物件概要1行（hero-subtitle）を更新
- [ ] リフォーム等の追加訴求文（hero-reform）を更新または削除

### ROOM CINEMAセクション
- [ ] YouTube VIDEO_ID を変更
- [ ] `youtube-nocookie.com/embed/VIDEO_ID` 形式になっているか確認
- [ ] `origin=` パラメータにVercelのURLを設定（デプロイ後に更新）
- [ ] `referrerpolicy="strict-origin-when-cross-origin"` が設定されているか
- [ ] `loading="lazy"` が設定されているか

### 室内紹介・ギャラリーセクション
- [ ] 全画像のパスを新物件用に更新
- [ ] インテリア配置写真に「家具配置イメージ / Interior Image」バッジが付いているか
- [ ] 現況写真とインテリアイメージを混同していないか
- [ ] ライトボックスのgalleryデータ配列（gAll / gCurrent / gInterior 等）を更新

### 設備仕様セクション
- [ ] 物件の設備に合わせてカードを追加・削除
- [ ] 絵文字アイコンを使っていないか（SVGラインアイコンのみ使用）
- [ ] 各カードに説明文があるか

### 共用部・周辺セクション
- [ ] 共用部写真（エントランス・ラウンジ等）のパスを更新
- [ ] 周辺写真のパスを更新
- [ ] 周辺施設リストを物件エリアに合わせて更新

### 物件概要テーブル
- [ ] 全項目（価格・間取り・専有面積・所在階・築年月・施工会社等）を更新
- [ ] 価格がゴールドカラー・大きめフォントで表示されているか

### お問い合わせフォーム
- [ ] Formspree action URL を `https://formspree.io/f/FORM_ID` に更新
- [ ] `_subject` hidden fieldの物件名を更新
- [ ] 送信成功モーダルのテキストを確認
- [ ] 送信失敗モーダルが機能するか確認

### フッター
- [ ] 物件名・住所を更新
- [ ] 問い合わせ先メールアドレスを更新
- [ ] 注意事項5項目を確認・必要に応じて変更

### スマホ固定CTA
- [ ] スマホで3ボタンが見切れていないか確認
- [ ] `env(safe-area-inset-bottom)` 対応しているか（iPhone notch対応）

---

## Phase 3：技術確認

- [ ] 横スクロールが発生しないか（スマホで確認）
- [ ] 全画像が表示されているか（パス確認）
- [ ] ライトボックスが開閉できるか（クリック・ESC・スワイプ）
- [ ] ギャラリータブ切り替えが機能するか
- [ ] フォームの必須バリデーションが機能するか
- [ ] スクロールアニメーション（fade-up / spec-card）が動くか
- [ ] ヒーロー動画が自動再生 → クロスフェードでループするか
- [ ] スマホでYouTube縦動画が左右にはみ出ていないか

---

## Phase 4：デプロイ

- [ ] `.gitignore` に `.claude/` が含まれているか
- [ ] `git add` → `git commit` → `git push`（`docs/` `templates/` も含める）
- [ ] Vercelで新リポジトリを接続・デプロイ
- [ ] デプロイ完了後、YouTube `origin=` を本番URLに更新 → 再push
- [ ] Formspreeダッシュボードで通知先メールを確認
- [ ] 本番URLでフォーム送信テストを実施（実際にメールが届くか）
- [ ] Vercel Analyticsのダッシュボードでページビューが計測されているか確認

---

## Phase 5：公開後確認

- [ ] スマホ（iPhone Safari）で全セクション確認
- [ ] PC（Chrome / Safari）で全セクション確認
- [ ] OGP画像がSNSシェア時に表示されるか（[opengraph.xyz](https://www.opengraph.xyz) で確認）
- [ ] YouTube埋め込みが正常に表示されるか（エラーなし）
- [ ] フォーム送信後のモーダルが正常に表示されるか
