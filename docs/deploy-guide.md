# GitHub / Vercel デプロイ手順

---

## 事前準備

- GitHubアカウント（例：`vicfirth219342-lgtm`）
- Vercelアカウント（GitHubでログイン推奨）
- GitHubに新規リポジトリを作成済みであること

---

## Step 1：Git 初期化とリモート設定

### 新規プロジェクトの場合

```bash
cd ~/Desktop/LP_photo       # プロジェクトフォルダへ移動
git init                    # Git初期化
git branch -M main          # ブランチをmainに設定
git remote add origin https://github.com/ユーザー名/リポジトリ名.git
```

### 既存リポジトリのリモートを切り替える場合

```bash
git remote set-url origin https://github.com/ユーザー名/新リポジトリ名.git
git remote -v               # 確認
```

---

## Step 2：.gitignore の確認

以下が含まれていることを確認：

```
.DS_Store
**/.DS_Store
.claude/
*.log
```

---

## Step 3：初回コミットとpush

```bash
# ユーザー情報設定（初回のみ）
git config user.email "your@email.com"
git config user.name "your-github-name"

# ステージング
git add index.html images/ docs/ templates/ CLAUDE.md .gitignore serve.py

# コミット
git commit -m "feat: 物件名 LP 初回リリース"

# push
git push -u origin main
```

**認証について**：パスワードではなく **Personal Access Token (PAT)** が必要。
GitHub → Settings → Developer settings → Personal access tokens で発行。

---

## Step 4：Vercel へのデプロイ

1. [vercel.com](https://vercel.com) にログイン（GitHubアカウントで連携推奨）
2. **「Add New → Project」** をクリック
3. GitHub連携でリポジトリを選択
4. 以下の設定を確認：
   - **Framework Preset**：`Other`（静的HTMLのためフレームワーク不要）
   - **Root Directory**：`/`（変更不要）
   - **Build Command**：空欄
   - **Output Directory**：空欄
5. **「Deploy」** をクリック

---

## Step 5：デプロイ後の更新作業

Vercelの本番URLが確定したら：

### YouTube origin= を更新

`index.html` の YouTube iframe の `origin=` パラメータを本番URLに変更：

```html
src="https://www.youtube-nocookie.com/embed/VIDEO_ID?rel=0&playsinline=1&enablejsapi=1&origin=https://あなたのプロジェクト名.vercel.app"
```

変更後：

```bash
git add index.html
git commit -m "fix: YouTube origin URLを本番環境に更新"
git push
```

Vercelは `main` ブランチへのpushを検知して自動で再デプロイする。

---

## Step 6：2回目以降の更新方法

```bash
# ファイル編集後
git add index.html          # 変更したファイルのみadd
git commit -m "fix: 内容の説明"
git push
```

Vercel側は自動でデプロイされるので追加操作不要。

---

## よくあるエラーと対処

| エラー | 原因 | 対処 |
|---|---|---|
| `fatal: unable to auto-detect email address` | gitのユーザー設定なし | `git config user.email` を実行 |
| `remote: Repository not found` | リポジトリURLが間違い | `git remote -v` で確認し修正 |
| `! [rejected] main -> main (non-fast-forward)` | リモートに変更がある | `git pull --rebase origin main` してから再push |
| 認証エラー | PATが無効 | GitHubでPATを再発行 |
| Vercelでファイルが404 | パスが間違い | ファイルパスを確認（大文字小文字・日本語フォルダ名） |
