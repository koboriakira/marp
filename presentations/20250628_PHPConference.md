---
marp: true
theme: phpcon2025
class: lead
paginate: true
--------------

# GitHub Copilot Agent を

# **“置くだけ”で終わらせない**

### 最初の 1 ファイルから自動レビューまで

**PHP Conference 2025**
Akira Kobori / @kobori_akira_pw

---

## なぜ今 AI エージェント?

* PR レビューに時間がかかる
* コードを書くより**待ち時間**が長い
* Copilot Agent で「書く＋レビュー」を自動化

<!-- Ask audience: 1 PR レビューに何分? 挙手 -->

---

## Copilot Agent 超速概要

![bg auto 80%](assets/agent-overview.png)

* **LLM**
* **Instructions ファイル**
* **Tools (CLI / API)**

> ChatGPT だけでは届かない“リポジトリ文脈”を注入

---

## 3 つのステップで始める

1. 全員導入 & `copilot-instructions.md`
2. Issue / PR プロンプトを置く
3. Copilot を自動レビュワー化

---

<!-- _class: lead -->

# STEP 1

## 全員導入 & 初期指示

---

### Why 全員導入?

| 分断が起きる 🙅‍♂️               | 全員が使う 🙆‍♂️              |
| ---------------------------- | ------------------------- |
| 使える / 使えない でモチベ差 | "再現できる" で議論が早い |

---

### `.github/copilot-instructions.md`

```md
# GitHub Copilot Agent Instructions

リポジトリ概要: PHP 8.3 / Laravel 11

- **やりとりは日本語**
- 返答は 80 行以内
- PSR-12 コーディング規約を守る
```

> *Live demo*: VS Code で作成 → git commit

---

<!-- _class: lead -->

# STEP 2

## Issue / PR プロンプト

---

### 面倒な作業から自動化

```
.github/prompts/github/
 ├─ create-issue.md
 └─ create-pr.md
```

* まず **Issue タイトル/本文** を自動生成
* PR は**二段構えテンプレ**で一次レビュー

---

#### 二段構えテンプレ (抜粋)

```md
<!-- Copilot raw output ↓ -->
...
<!-- Copilot raw output ↑ -->

### ✂ Curated Description (編集必須)
- 変更サマリ (50 行以内)
- 影響範囲 / テスト結果

- [ ] I have reviewed & trimmed the Copilot output.
```

---

<!-- _class: lead -->

# STEP 3

## Copilot を自動レビュワー化

---

### CODEOWNERS で Bot を指名

```
* @github-copilot
```

`branch protection`: **"Copilot review ✓" 必須**

---

### GitHub Actions ワークフロー

```yaml
name: Copilot Review
on: [pull_request]
permissions:
  pull-requests: write
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Copilot Review
        run: ./scripts/copilot-review.sh
```

Bot が PR にコメント ➜ 作成者が修正 ➜ 人間は設計を見る

---

## メトリクスで効果を測る

* `raw_chars / curated_chars` 比率
* Copilot 指摘採用率
* Build fail → fix までの時間

> 週次 15 分レトロで共有

---

## Pitfalls & Guardrails

1. **長文放置** → 二段構え＋CI ゲート
2. **シークレット流出** → Diff マスク
3. **コンテキスト肥大** → RAG or ファイル分割

---

<!-- _class: lead -->

# 3 Key Takeaways

1. **まず 1 ファイル** を全員コミット
2. **一次レビューは開発者＋Bot**
3. **数字で効果を測って回す**

---

## 24h チャレンジ

1. `copilot-instructions.md` を作る
2. Issue/PR プロンプトを置く
3. 次の PR で **Copilot Review Bot** を走らせる

👉 サンプル & スクリプトは **QR コード** から

![bg right 40%](assets/qr-placeholder.png)

---

# Q & A

*時間いっぱいまでどうぞ！*

---

<!-- presenter notes -->

notes:

* タイマーを 25:00 でスタート
* Demo 時間: Step1 slide 2 分以内、Step2 before/after 1.5 分
* If Q\&A なし→Takeaways 再掲で調整
