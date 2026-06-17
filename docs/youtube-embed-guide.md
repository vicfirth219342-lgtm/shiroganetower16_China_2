# YouTube 埋め込みガイド（エラー153対策含む）

---

## 基本ルール

静的HTMLのLPにYouTube動画（特にShorts）を埋め込む際の必須事項。

### 使用するURL形式

```
✅ 正しい：https://www.youtube-nocookie.com/embed/VIDEO_ID
❌ 禁止：  https://youtube.com/shorts/VIDEO_ID?feature=share
❌ 非推奨：https://www.youtube.com/embed/VIDEO_ID  （nocookieなし）
```

---

## 実装テンプレート（9:16縦動画 / Shorts対応）

```html
<!-- 9:16縦動画コンテナ -->
<div style="
  width: min(360px, 90vw);
  aspect-ratio: 9 / 16;
  position: relative;
  overflow: hidden;
  margin: 0 auto;
">
  <!--
    ここにYouTube Shorts動画URLを設定
    VIDEO_IDを差し替えてください
    origin= にはVercelの本番URLを設定
  -->
  <iframe
    id="room-cinema-video"
    src="https://www.youtube-nocookie.com/embed/VIDEO_ID?rel=0&playsinline=1&enablejsapi=1&origin=https://あなたのプロジェクト名.vercel.app"
    title="動画タイトル"
    loading="lazy"
    referrerpolicy="strict-origin-when-cross-origin"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowfullscreen
    style="position: absolute; inset: 0; width: 100%; height: 100%; border: none;"
  ></iframe>
</div>
```

---

## エラー153 の原因と対処

### エラー153とは

YouTube iframe埋め込み時に「動画を再生できません」と表示されるエラー。
公式の定義済みエラーコードではないが、主に以下の原因で発生する。

### 原因一覧と対処

| 原因 | 対処 |
|---|---|
| `youtube.com/shorts/` URLをそのまま使用 | `youtube-nocookie.com/embed/VIDEO_ID` に変換 |
| `origin=` パラメータがない | デプロイ先URLを `origin=` に設定 |
| `referrerpolicy` が設定されていない | `referrerpolicy="strict-origin-when-cross-origin"` を追加 |
| ローカル環境で `origin=` がミスマッチ | ローカルでは503/エラーが出ることがある（本番で確認） |
| `youtube.com/embed/` （non-nocookie）を使用 | `youtube-nocookie.com` ドメインに変更 |
| 動画の埋め込みが動画オーナーにより無効化 | 動画の設定で「埋め込みを許可」を確認 |

---

## YouTube Shorts URLから埋め込みURLへの変換

| 元URL | 変換後 |
|---|---|
| `https://youtube.com/shorts/lAEytdS0mnE?feature=share` | `https://www.youtube-nocookie.com/embed/lAEytdS0mnE` |
| `https://www.youtube.com/watch?v=VIDEOID` | `https://www.youtube-nocookie.com/embed/VIDEOID` |

**VIDEO_IDの取り出し方**：URLの最後の英数字部分（例：`lAEytdS0mnE`）

---

## URLパラメータの説明

| パラメータ | 値 | 説明 |
|---|---|---|
| `rel` | `0` | 関連動画を非表示（同チャンネルのみ表示） |
| `playsinline` | `1` | iOSでインライン再生（フルスクリーン強制を防ぐ） |
| `enablejsapi` | `1` | YouTube Player APIを有効化（再生追跡に必要） |
| `origin` | `https://あなたのURL` | クロスオリジン通信の許可（**必須**） |
| `modestbranding` | `1` | YouTubeロゴを最小化（廃止予定のため省略可） |

---

## YouTube Player APIで再生を計測する

```js
// YouTube IFrame Player APIを読み込む
const tag = document.createElement('script');
tag.src = 'https://www.youtube.com/iframe_api';
document.head.appendChild(tag);

let tracked = false;
window.onYouTubeIframeAPIReady = function() {
  new YT.Player('room-cinema-video', {
    events: {
      onStateChange: function(e) {
        if (e.data === YT.PlayerState.PLAYING && !tracked) {
          tracked = true;
          // Google Analyticsへ送信
          if (typeof gtag !== 'undefined') {
            gtag('event', 'video_play', {
              event_category: 'room_cinema',
              event_label: 'room_tour'
            });
          }
          // Vercel Analyticsへ送信
          if (window.va) {
            window.va('event', { name: 'video_play' });
          }
        }
      }
    }
  });
};
```

---

## チャンネル名が旧名称のまま表示される場合

これはLP側（HTML/CSS）では対処不可能。

- YouTubeのサーバー側がキャッシュしているチャンネル情報が表示されている
- 通常1〜3日で自動的に反映される
- LP側のコードを変更しても解決しない
- YouTubeで「チャンネル名を一度変更 → 元に戻す」で更新が早まることがある

---

## ローカル環境での注意

```
origin=https://本番URL.vercel.app
```

このパラメータはローカル（`localhost` / `file://`）では一致しないため、
ローカルプレビューでiframeがブロックされる場合がある。

→ **本番動作確認はVercel本番URLで行うこと**
