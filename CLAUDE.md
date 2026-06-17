# 白金タワーLP — Claude Code 制作マニュアル

このプロジェクトは「白金タワー（港区白金1丁目）」の売主専用ランディングページ。
このファイルを読むだけで、同じ品質の高級不動産LPをゼロから制作・修正できるよう整理している。

---

## 1. このLPの制作方針

- 目的：購入検討者から**問い合わせを獲得**するための、1物件専用LP
- ターゲット：港区白金・高輪エリアの高額物件を検討する富裕層
- 競合との差別化：不動産ポータルではなく「**高級ホテルのブランドサイト**」の世界観
- 主役コンテンツ：ROOM CINEMAのルームツアー動画（YouTube Shorts埋め込み）
- コンバージョン：Formspreeの問い合わせフォーム送信

---

## 2. ROOM CINEMAらしい世界観

ROOM CINEMAは「映画のような余韻でその部屋を体験させる」コンセプト。

- **ヒーロー**：AI生成シネマティック動画をフルスクリーン背景に複数本クロスフェードで再生
- **ルームツアー**：YouTube Shorts（9:16縦動画）を中央配置。写真より先に動画を見せる設計
- **トーン**：無音・静寂・余白。テキストは最小限。写真と動画が語る
- **コピーライティング**：詩的で短く、改行を意識した「読む映像」のような言葉
- **アニメーション**：フェードアップのみ。派手なトランジションは禁止

---

## 3. 高級不動産LPのデザインルール

### カラーパレット

```css
--gold:       #C5A55A;   /* シャンパンゴールド（メインアクセント） */
--gold-light: #D4BC82;
--gold-dark:  #A8883E;
--white:      #FFFFFF;
--off-white:  #F7F5F1;   /* 明るいセクション背景 */
--black:      #0A0A0A;   /* ダークセクション背景 */
--ink:        #1A1A1A;
--gray-dark:  #1E1E1E;
--gray-mid:   #5C5C5C;
```

**禁止カラー**：黄色すぎるゴールド（#FFD700系）、明るいオレンジ、蛍光色

### フォント

```css
--serif: "Noto Serif JP","Hiragino Mincho ProN","Yu Mincho",serif;
--sans:  "Hiragino Sans","Noto Sans JP","Helvetica Neue",Arial,sans-serif;
```

- 見出し・キャッチコピー・物件名：**serif**
- ラベル・キャプション・説明文：**sans**
- 英語ラベル（ROOM CINEMA等）：sans、letter-spacing: .2〜.3em、uppercase

### 余白

```css
--section-pad: clamp(80px, 10vw, 140px);  /* セクション上下 */
```

- セクション間は必ず大きな余白。「窮屈」は禁止
- 要素間の余白は `clamp()` で流動的に設定
- ヒーローの文字は下寄せ（`align-items: flex-end`）

---

## 4. ヒーローセクションの作り方

### 構造

```html
<section id="hero">
  <!-- A/B 2枚のvideoをクロスフェード -->
  <video id="hero-vid-a" class="hero-video active" muted playsinline preload="auto" poster="フォールバック画像">
    <source src="動画1.mp4" type="video/mp4">
  </video>
  <video id="hero-vid-b" class="hero-video" muted playsinline preload="none">
    <source src="動画2.mp4" type="video/mp4">
  </video>
  <div class="hero-overlay"></div>
  <div class="hero-content">
    <div class="hero-badge">物件英語ラベル</div>
    <h1 class="hero-title">キャッチコピー</h1>
    <p class="hero-subtitle">物件概要1行</p>
    <p class="hero-reform">リフォーム等の追加訴求</p>
    <div class="hero-btns">CTAボタン群</div>
  </div>
</section>
```

### CSS要点

```css
.hero-video { position:absolute; inset:0; width:100%; height:100%; object-fit:cover; opacity:0; transition:opacity 1.2s ease; }
.hero-video.active { opacity:1; }
.hero-overlay { background: linear-gradient(to top, rgba(0,0,0,.9) 0%, rgba(0,0,0,.3) 55%, rgba(0,0,0,.15) 100%); }
```

### JS（クロスフェードループ）

```js
const VIDEOS = ['動画1.mp4', '動画2.mp4', '動画3.mp4', '動画4.mp4'];
let idx = 0;
const vidA = document.getElementById('hero-vid-a');
const vidB = document.getElementById('hero-vid-b');
let active = vidA, standby = vidB;

function nextVideo() {
  idx = (idx + 1) % VIDEOS.length;
  standby.src = VIDEOS[idx];
  standby.load();
  standby.play().then(() => {
    standby.classList.add('active');
    active.classList.remove('active');
    const prev = active;
    active = standby; standby = prev;
    setTimeout(() => { prev.pause(); prev.src = ''; }, 1400);
  });
}
vidA.play();
vidA.addEventListener('ended', nextVideo);
vidB.addEventListener('ended', nextVideo);
```

### キャッチコピーの改行ルール

```css
.hero-title {
  font-size: clamp(24px, 3.8vw, 52px);
  line-height: 1.65;
  word-break: keep-all;
  overflow-wrap: normal;
  line-break: strict;
  max-width: 820px;
}
```

---

## 5. ROOM CINEMA 動画セクションの作り方

### YouTube Shorts埋め込みルール

```html
<!-- 必ずyoutube-nocookie.comのembed形式を使用 -->
<div style="width:min(360px,90vw); aspect-ratio:9/16; position:relative; overflow:hidden;">
  <iframe
    src="https://www.youtube-nocookie.com/embed/VIDEO_ID?rel=0&playsinline=1&enablejsapi=1&origin=https://あなたのVercelURL.vercel.app"
    title="タイトル"
    loading="lazy"
    referrerpolicy="strict-origin-when-cross-origin"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowfullscreen
    style="position:absolute; inset:0; width:100%; height:100%; border:none;"
  ></iframe>
</div>
```

**禁止**：`https://youtube.com/shorts/VIDEO_ID?feature=share` をそのまま使用
**禁止**：`https://www.youtube.com/embed/` ← nocookieなし（エラー153の原因になる場合あり）

### 配置設計

- ファーストビュー直下に設置（写真より先に動画を見せる）
- PC：`width: min(360px, 90vw)` で中央配置
- スマホ：`width: 90vw` で横幅フル活用
- 動画下に3つのCTAボタンを必ず配置

---

## 6. 物件概要セクションの作り方

```html
<table class="info-table">
  <tr><th>販売価格</th><td><span class="info-price">XX,XXX万円</span></td></tr>
  <tr><th>物件名</th><td>○○タワー</td></tr>
  <tr><th>所在地</th><td>東京都○○区</td></tr>
  <tr><th>交通</th><td>○○駅 徒歩X分</td></tr>
  <tr><th>間取り</th><td>XLDKなど</td></tr>
  <tr><th>専有面積</th><td>XX.XX㎡</td></tr>
  <!-- 以下続く -->
</table>
```

**価格のスタイル**：serif、ゴールド、大きめフォント（`clamp(22px, 3vw, 34px)`）

---

## 7. 設備仕様セクションの作り方

**禁止**：絵文字アイコン、表組みレイアウト（不動産ポータル感が出る）

### カードデザイン

```css
.spec-card {
  background: #FFFFFF;
  border-radius: 22px;
  padding: clamp(28px, 3.5vw, 44px) clamp(18px, 2.5vw, 28px);
  box-shadow: 0 2px 12px rgba(0,0,0,.06), 0 0 0 1px rgba(0,0,0,.04);
  transition: transform .35s ease, box-shadow .35s ease;
}
.spec-card:hover {
  transform: translateY(-6px);
  box-shadow: 0 16px 40px rgba(0,0,0,.1), 0 0 0 1px rgba(185,155,94,.2);
}
```

- アイコン：SVGラインアイコン（Lucide/Heroicons準拠）、ゴールド系
- 設備名 + 1〜2行の説明文を必ずセットで記載
- グリッド：PC 4列、タブレット 3列、スマホ 2列、極小 1列
- スクロール時：カードごとに 0.055秒 stagger でフェードアップ

---

## 8. Formspree問い合わせフォームの設定方法

```html
<form
  id="contact-form"
  action="https://formspree.io/f/あなたのFORM_ID"
  method="POST"
>
  <input type="text"  name="name"    required>
  <input type="email" name="email"   required>
  <input type="tel"   name="tel">
  <textarea           name="message" required></textarea>
  <input type="hidden" name="_subject" value="【物件名】お問い合わせ">
  <button type="submit">送信する</button>
</form>
```

### JS送信処理（fetch）

```js
form.addEventListener('submit', async (e) => {
  e.preventDefault();
  const res = await fetch(form.action, {
    method: 'POST',
    body: new FormData(form),
    headers: { 'Accept': 'application/json' }
  });
  if (res.ok) {
    // 成功モーダル表示、form.reset()
  } else {
    // エラーモーダル表示
  }
});
```

- 成功時：`alert()` 禁止 → モーダル表示
- 失敗時：`alert()` 禁止 → エラーモーダル表示
- Formspreeダッシュボードで通知先メールを必ず設定

---

## 9. GitHub / Vercelデプロイ時の注意点

### Gitignore必須項目

```
.DS_Store
**/.DS_Store
.claude/
*.log
```

### push前チェック

- `index.html` の YouTube `origin=` パラメータをVercelの本番URLに変更したか
- Formspree ID が `YOUR_FORM_ID` のまま残っていないか
- Google Analytics ID が `G-XXXXXXXXXX` のまま残っていないか

### Vercel設定

- Framework Preset：**Other**（静的HTMLなのでNext.js等は選択しない）
- Root Directory：変更不要（`/` のまま）
- Build Command：なし
- Output Directory：なし

---

## 10. 画像・動画素材の保存場所

```
images/
├─ 現状写真/          # 実際の室内・外観写真
├─ インテリア配置写真/ # 家具配置後のイメージ（AI生成含む）
├─ 共用部分/          # エントランス・ラウンジ等
└─ 周辺写真/          # 駅・公園・周辺施設
```

**命名規則**：スペースなし、日本語フォルダ名はOK、mp4は `hero_video1.mp4` のような連番
**ヒーロー動画**：`インテリア配置写真/hero_video1.mp4` を起点に命名
**インテリア配置写真の注意**：LP上に表示する際は必ず「家具配置イメージ / Interior Image」バッジを付与

---

## 11. レスポンシブ対応ルール

| ブレークポイント | 対応内容 |
|---|---|
| `max-width: 1024px` | 設備カード 4列→3列 |
| `max-width: 768px` | 2カラムグリッド→1カラム |
| `max-width: 640px` | ギャラリー 3列→2列、ヘッダーCTA非表示 |
| `max-width: 480px` | ルームグリッド1列化、設備カード2列 |
| `max-width: 400px` | 設備カード1列 |

**スマホ固定CTA**：`position: fixed; bottom: 0;` に3ボタン配置（`env(safe-area-inset-bottom)` 対応）
**YouTube縦動画**：`width: min(360px, 90vw); aspect-ratio: 9/16;`（絶対にはみ出さない）
**横スクロール禁止**：`overflow-x: hidden` を `html` と `body` 両方に指定

---

## 12. SEO / OGP設定

```html
<title>物件名｜最寄り駅徒歩X分｜間取り・面積</title>
<meta name="description" content="最寄り駅徒歩X分。物件名XX階部分、間取り・面積。リフォーム有無。アドレスのタワーレジデンス。">

<!-- OGP -->
<meta property="og:title"       content="物件名｜最寄り駅徒歩X分">
<meta property="og:description" content="上記descriptionと同じ">
<meta property="og:type"        content="website">
<meta property="og:image"       content="最も魅力的なインテリア画像のURL">
<meta name="twitter:card"       content="summary_large_image">
```

**og:image**：インテリア配置写真の中で最も映えるものを選ぶ（現況写真より映えを優先）

---

## 13. やってはいけないこと

| 禁止事項 | 理由 |
|---|---|
| 絵文字アイコン使用 | 安っぽく見える |
| 表組みで設備を列挙 | 不動産ポータル感が出る |
| `alert()` でエラー表示 | UXが壊れる |
| `youtube.com/shorts/` をそのままiframeに | エラー153の原因 |
| `youtube.com/embed/` をnon-nocookieで使用 | プライバシー問題・エラーの可能性 |
| ヒーロー動画に `loop` 属性 | 1本しかループしない（複数動画が活かせない） |
| `word-break: break-all` | 日本語が単語途中で切れる |
| `alert()` で送信完了通知 | 即座に消えて確認できない |
| 絶対パスで画像指定（デプロイ後に壊れる） | — |
| `.claude/` をgit管理 | セッション固有の設定が混入する |

---

## 14. 次回別物件LPを作る時の作業手順

1. **物件情報をまとめる**（`templates/property-lp-brief.md` を使用）
2. **画像・動画素材を整理**して `images/` 以下に格納
3. **`index.html` をコピー**してファイルパスと文言を置換
4. **ヒーロー動画のパスを更新**
5. **YouTubeのVIDEO_IDを変更**し、`origin=` をVercel URLに設定
6. **Formspreeで新しいフォームを作成**してFORM_IDを差し替え
7. **物件概要テーブルを更新**
8. **設備仕様カードを物件に合わせて追加・削除**
9. **ギャラリーの画像パスを全更新**
10. **SEO/OGP（title・description・og:image）を更新**
11. **`.gitignore` を確認**し `git push`
12. **Vercelで新リポジトリを接続**してデプロイ
13. **YouTube `origin=`** をVercel本番URLに更新して再push

詳細は `docs/lp-production-checklist.md` を参照。
