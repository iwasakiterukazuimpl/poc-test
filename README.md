# poc-test

社内向けPoC（試作品）を、ブラウザで手軽に確認できるようにするためのリポジトリです。
`index.html` を開くと、リポジトリ内のPoCが自動で一覧表示されます。

**公開URL:** https://iwasakiterukazuimpl.github.io/poc-test/

---

## リポジトリ構成

```
poc-test/
├── index.html          ← PoC一覧ページ（自動更新・編集不要）
├── brand-guideline.html ← ブランドカラー・タイポグラフィのガイドライン
├── poc-sample/
│   └── index.html       ← 各PoC（フォルダ名は poc- から始める）
└── poc-xxxx/
    └── index.html
```

---

## 新しいPoCを追加する方法

**ルールは1つだけ：フォルダ名を `poc-` から始めること。**

1. `poc-` から始まる名前でフォルダを作る（例: `poc-login-flow`）
2. そのフォルダの直下に `index.html` を置く（表示したいモックそのもの）
3. リポジトリにpushする

以上です。一覧ページ（`index.html`）は編集不要です。次にトップページを開いたタイミングで、GitHub APIからフォルダ一覧を自動取得し、`poc-` で始まるものだけをカードとして表示します。

```
poc-test/
└── poc-login-flow/     ← これを追加してpushするだけ
    └── index.html
```

> フォルダ名がそのままカードのタイトルになります（例: `poc-login-flow` → 「Login Flow」）。ハイフン・アンダースコアはスペースに変換され、単語の先頭が大文字になります。

---

## GitHub Pagesの設定

このリポジトリは **Public** にする必要があります（Freeプランでは、Privateリポジトリの状態だとGitHub Pagesが使えません）。

設定手順（初回のみ・すでに設定済み）：

1. リポジトリの **Settings** タブを開く
2. 左メニューの **Pages** を選択
3. **Build and deployment** の **Source** で `Deploy from a branch` を選択
4. **Branch** で `main` を選び、フォルダは `/ (root)` を選択
5. **Save**

保存後、`main` ブランチにpushするたびに自動でビルド・デプロイされます（追加の操作は不要、1〜2分で反映）。

デプロイ状況は、リポジトリの **Actions** タブから `pages build and deployment` のワークフローで確認できます。

---

## 一覧ページ（index.html）の仕組み

- ページを開くと、GitHub API（`GET /repos/{owner}/{repo}/contents/`）を叩いてリポジトリ直下の一覧を取得
- `type: "dir"` かつ名前が `poc-` から始まるものだけを抽出してカード表示
- 未認証でのAPI呼び出しのため、**1時間あたり60回**までのアクセス制限あり（個人利用の範囲では通常問題なし）

もしリポジトリ名やアカウント名を変更・フォークした場合は、`index.html` の下部にある以下の2行を書き換えてください。

```javascript
const OWNER = "iwasakiterukazuimpl";
const REPO = "poc-test";
```

---

## デザイン・配色ルール

トップページや各PoCのUIで色・フォントを使う際は、`brand-guideline.html` を参照してください。ブランドカラー（`#87b828`）を基準にした配色トークンと、Do/Don't（コントラスト比の基準など）をまとめています。

主なポイントだけ抜粋:

| 用途 | トークン | 値 |
|---|---|---|
| ブランドアクセント（塗り背景） | `--brand-500` | `#87b828` |
| 文字・線として使う場合（白背景でAA基準クリア） | `--brand-700` | `#56761d` |
| 見出しフォント | `--font-display` | Space Grotesk + Noto Sans JP |
| 本文フォント | `--font-body` | Inter + Noto Sans JP |
| ラベル・コード用 | `--font-mono` | JetBrains Mono |

> `brand-500` をそのまま文字色や線に使うとコントラスト比が2.35:1程度しかなくAA基準（4.5:1）を満たさないため、テキスト用途では必ず `brand-700` を使うこと。

---

## トラブルシューティング

**Pagesを開くと404になる**
- 対象フォルダの直下に `index.html` があるか確認
- Settings → Pages で `main` ブランチ・`/root` が選択されているか確認

**一覧に「一覧の取得に失敗しました」と出る**
- `index.html` 内の `OWNER` / `REPO` が実際の値になっているか確認
- 短時間に何度もリロードしているとAPIのレート制限（60回/時）に達している可能性あり。1時間待つか、時間を置いて再度確認

**pushしたのに反映されない**
- Actionsタブでビルドが失敗していないか確認（赤いバツがあればログを確認）
- ブラウザキャッシュが原因のことが多いので、シークレットウィンドウで開き直す
