# デザインシステム — 高級不動産 LP

白金タワーLPで確立したデザインシステム。次回以降のLP制作でそのまま流用可能。

---

## カラーパレット

### プライマリ

| 変数名 | カラーコード | 用途 |
|---|---|---|
| `--gold` | `#C5A55A` | メインアクセント（CTAボタン・ラベル・ライン） |
| `--gold-light` | `#D4BC82` | ホバー時のゴールド |
| `--gold-dark` | `#A8883E` | ライトセクション上のゴールド |
| `--black` | `#0A0A0A` | ダークセクション背景 |
| `--ink` | `#1A1A1A` | フッター背景・テキスト |
| `--gray-dark` | `#1E1E1E` | 中間ダークセクション背景 |
| `--off-white` | `#F7F5F1` | ライトセクション背景 |
| `--white` | `#FFFFFF` | カード・テーブル背景 |
| `--gray-mid` | `#5C5C5C` | サブテキスト（ライトセクション） |

### 設備カード専用パレット

```css
背景：    #F7F5F2
カード：  #FFFFFF
アクセント：#B89B5E
文字：    #1F1F1F
サブテキスト：#6B6B6B
```

---

## タイポグラフィ

### フォントスタック

```css
/* 見出し・キャッチコピー・物件名・価格 */
--serif: "Noto Serif JP","Hiragino Mincho ProN","Yu Mincho",serif;

/* ラベル・説明文・UI要素 */
--sans: "Hiragino Sans","Noto Sans JP","Helvetica Neue",Arial,sans-serif;
```

### サイズスケール

| 用途 | CSS |
|---|---|
| ヒーローキャッチコピー | `clamp(24px, 3.8vw, 52px)` |
| セクション見出し | `clamp(22px, 3.5vw, 38px)` |
| 物件価格 | `clamp(22px, 3vw, 34px)` |
| 本文 | `clamp(13px, 1.5vw, 15px)` |
| セクションラベル（英語） | `10px` / `letter-spacing: .3em` / uppercase |
| カードタイトル | `clamp(13px, 1.4vw, 15px)` |
| カード説明文 | `11px` |
| キャプション・注釈 | `10px` |

### 行間

| 用途 | line-height |
|---|---|
| ヒーロータイトル | `1.65` |
| セクション見出し | `1.55` |
| 本文・説明文 | `2.1〜2.2` |
| カード説明文 | `1.8` |

### 日本語テキストの改行制御

```css
/* 見出しに必ず適用 */
word-break: keep-all;
overflow-wrap: normal;
line-break: strict;
```

---

## 余白システム

```css
--section-pad: clamp(80px, 10vw, 140px);  /* セクション上下 */
```

### セクション横幅

```css
/* コンテンツ最大幅 */
max-width: min(1200px, 90vw);
margin-inline: auto;

/* セクション横パディング */
padding: var(--section-pad) clamp(24px, 6vw, 100px);
```

---

## ボタン

```css
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 16px 34px;
  font-family: var(--serif);
  font-size: 12px;
  letter-spacing: .18em;
  transition: all .35s ease;
  border: none;
}

/* ゴールド塗りつぶし（メインCTA） */
.btn-gold    { background: #C5A55A; color: #0A0A0A; }
.btn-gold:hover { background: #D4BC82; }

/* ゴールドアウトライン（サブCTA） */
.btn-outline    { background: transparent; color: #C5A55A; border: 1px solid #C5A55A; }
.btn-outline:hover { background: #C5A55A; color: #0A0A0A; }
```

---

## カードコンポーネント（設備仕様）

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

### アイコンコンテナ

```css
.spec-card-icon {
  width: 48px;
  height: 48px;
  border-radius: 14px;
  background: rgba(185,155,94,.1);
  display: flex;
  align-items: center;
  justify-content: center;
}
/* SVGアイコン設定 */
svg {
  width: 22px; height: 22px;
  stroke: #B89B5E; stroke-width: 1.6;
  fill: none; stroke-linecap: round; stroke-linejoin: round;
}
```

---

## 画像・映像コンポーネント

### ヒーロー（フルスクリーン動画）

```css
.hero-video {
  position: absolute; inset: 0;
  width: 100%; height: 100%;
  object-fit: cover;
  opacity: 0;
  transition: opacity 1.2s ease;
}
.hero-video.active { opacity: 1; }
```

### ギャラリーカード（ホバーズーム）

```css
.g-item img {
  transition: transform .6s cubic-bezier(.22,.61,.36,1);
}
.g-item:hover img { transform: scale(1.06); }
```

### YouTube縦動画コンテナ

```css
.cinema-video-inner {
  width: min(360px, 90vw);
  aspect-ratio: 9 / 16;
  position: relative; overflow: hidden;
  box-shadow: 0 32px 90px rgba(0,0,0,.75), 0 0 0 1px rgba(197,165,90,.15);
}
```

---

## アニメーション

### フェードアップ（共通）

```css
.fade-up {
  opacity: 0;
  transform: translateY(28px);
  transition: opacity .85s cubic-bezier(.22,.61,.36,1),
              transform .85s cubic-bezier(.22,.61,.36,1);
}
.fade-up.visible { opacity: 1; transform: none; }
```

### 設備カード stagger

```js
document.querySelectorAll('.spec-card').forEach((card, i) => {
  card.style.transitionDelay = (i * 0.055) + 's';
});
```

### Intersection Observer 設定

```js
const obs = new IntersectionObserver(entries => {
  entries.forEach(e => {
    if (e.isIntersecting) {
      e.target.classList.add('visible');
      obs.unobserve(e.target);
    }
  });
}, { threshold: 0.1 });
```

---

## セクション構成パターン

| セクション背景 | 用途 |
|---|---|
| `#0A0A0A`（黒） | ヒーロー・ROOM CINEMA・ルーム紹介・ギャラリー・問い合わせ |
| `#F7F5F1`（オフホワイト） | アクセス・設備仕様・物件概要 |
| `#1E1E1E`（ダークグレー） | タワー紹介・リフォーム・資産性 |
| `#1E1E1E`（ダークグレー） | 共用部 |

明暗を交互に切り替えることで、スクロール時のリズムが生まれる。

---

## グリッドシステム

### 2カラム（テキスト + 画像）

```css
display: grid;
grid-template-columns: 1fr 1fr;
gap: clamp(40px, 6vw, 100px);
align-items: center;
```

### ルームグリッド（不均一）

```css
grid-template-columns: repeat(3, 1fr);
/* large カード */
.room-card.large { grid-column: span 2; aspect-ratio: 8/5; }
```

### 設備カードグリッド

```css
grid-template-columns: repeat(4, 1fr);
gap: clamp(14px, 2vw, 24px);
@media(max-width:1024px) { repeat(3,1fr) }
@media(max-width:640px)  { repeat(2,1fr) }
@media(max-width:400px)  { 1fr }
```
