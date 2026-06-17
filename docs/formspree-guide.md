# Formspree 問い合わせフォーム設定手順

静的HTML（Vercelデプロイ）でメール受信するための設定方法。

---

## Step 1：Formspreeでフォームを作成

1. [formspree.io](https://formspree.io) にアクセスしてアカウント作成（Googleログイン可）
2. ダッシュボードで **「+ New Form」** をクリック
3. フォーム名を入力（例：`白金タワー お問い合わせ`）
4. **通知先メールアドレス**を入力（例：`id_home219342@yahoo.co.jp`）
5. 作成完了後に表示される **Form ID**（例：`xdavveyo`）をメモ

---

## Step 2：HTMLに設定

```html
<form
  id="contact-form"
  action="https://formspree.io/f/あなたのFORM_ID"
  method="POST"
>
  <input type="text"  name="name"    required placeholder="山田 太郎">
  <input type="email" name="email"   required placeholder="example@email.com">
  <input type="tel"   name="tel"               placeholder="090-0000-0000">
  <textarea           name="message" required  placeholder="ご相談内容をご記入ください"></textarea>

  <!-- メール件名（任意） -->
  <input type="hidden" name="_subject" value="【物件名】お問い合わせ">

  <button type="submit">送信する</button>
</form>
```

**重要**：`action="https://formspree.io/f/FORM_ID"` の `FORM_ID` 部分のみ差し替え

---

## Step 3：JS送信処理（fetchで非同期送信）

`method="POST"` だけだとページ遷移してしまうため、fetchで非同期送信する。

```js
const form = document.getElementById('contact-form');
form.addEventListener('submit', async function(e) {
  e.preventDefault();
  const btn = form.querySelector('button[type=submit]');
  btn.textContent = '送信中...';
  btn.disabled = true;

  try {
    const res = await fetch(form.action, {
      method: 'POST',
      body: new FormData(form),
      headers: { 'Accept': 'application/json' }
    });

    if (res.ok) {
      // 成功モーダルを表示
      document.getElementById('success-modal').classList.add('open');
      form.reset();
      // コンバージョン計測
      if (typeof gtag !== 'undefined') {
        gtag('event', 'form_submit', { event_category: 'contact' });
      }
    } else {
      // エラーモーダルを表示
      document.getElementById('error-modal').classList.add('open');
    }
  } catch(err) {
    document.getElementById('error-modal').classList.add('open');
  }

  btn.textContent = '送信する';
  btn.disabled = false;
});
```

---

## Step 4：送信確認メールの設定（任意）

Formspreeダッシュボード → フォーム設定 → **「Auto-Response」** から、
送信者に自動返信メールを送ることができる（無料プランでも設定可能）。

---

## フォーム項目とname属性

| 項目 | name属性 | 必須 |
|---|---|---|
| お名前 | `name` | 必須 |
| メールアドレス | `email` | 必須 |
| 電話番号 | `tel` | 任意 |
| お問い合わせ内容 | `message` | 必須 |
| メール件名（hidden） | `_subject` | 推奨 |

---

## Formspreeのプラン

| プラン | 月間送信数 | 料金 |
|---|---|---|
| Free | 50件 | 無料 |
| Gold | 1,000件 | $10/月 |
| Platinum | 無制限 | $40/月 |

不動産LP（問い合わせ数が月50件以下）であれば**無料プランで十分**。

---

## よくあるトラブル

| 問題 | 原因 | 対処 |
|---|---|---|
| 送信しても通知が来ない | 迷惑メールフォルダに振り分け | Formspreeのドメインを許可リストに追加 |
| 送信ボタンを押すとページが遷移する | fetchなしのPOST送信 | 上記JS実装を使用する |
| `res.ok` が false になる | FORM_IDが間違い | Formspreeダッシュボードでエンドポイントを再確認 |
| Vercelで動かない | CORS設定 | Formspreeはデフォルトでどのドメインからも受信可能（問題なし） |
