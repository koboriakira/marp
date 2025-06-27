---
marp: true
theme: phpcon2025
class: lead
paginate: true
--------------

<style>
/* このスライド専用のカスタムスタイル */

/* フォント設定 */
section {
  font-family: 'M PLUS 1p', 'Hiragino Kaku Gothic ProN', 'Helvetica Neue', Arial, sans-serif;
}

/* 見出しのスタイル調整 */
h1 {
  color: #01a3c1;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.1);
}

h2 {
  color: #4d4c4b;
  border-bottom: 2px solid #01a3c1;
  padding-bottom: 10px;
}

/* コードブロックのスタイル */
pre {
  background: #f8f9fa;
  border-left: 4px solid #01a3c1;
  padding: 15px;
  border-radius: 5px;
}

/* リストのスタイル */
ul li {
  margin-bottom: 8px;
}

/* 強調テキスト */
strong {
  color: #01a3c1;
  font-weight: bold;
}

/* セカンダリテキスト（重要度の低いテキスト） */
.secondary {
  color: #828282;
}

/* QRコードやFIXMEコメント用のスタイル */
section:has(<!-- FIXME */) {
  position: relative;
}

/* カスタムクラス例 */
.highlight-box {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
}

.demo-note {
  background: #fff3cd;
  border: 1px solid #ffeaa7;
  padding: 10px;
  border-radius: 5px;
  font-size: 0.9em;
}

/* タイトルのみのスライドをセンタリング */
section:has(h1:only-child) {
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
}

section:has(h1:only-child) h1 {
  margin: 0;
}
</style>

# Akira Kobori

# **AIエージェントはこう育てる**

### GitHub Copilot Agentとチームの共進化サイクル

**PHP Conference 2025**

<!-- FIXME: スライドのQRコードを入れること -->

---

# 最初に

## Claude Codeについて

- 2025年2月
  - 弊チームのGitHub Copilot Agent導入 
- 2025年5月
  - 本トーク採択
  - **Claude Code発表**

2025年6月時点では、Claude Codeの導入検討をすべきかもしれない

---

# 結論

- AIエージェントの利用は**全員で**やること
- **IssueやPRの作成**から改善していく
- **Copilot用のドキュメント**のメンテナンスに注力する
  - 完璧を目指すのではなく、指示が上手くいかないタイミングでメンテしていく

---

# GitHub Copilot Agentとは

* 通常のGitHub CopilotのEditモードよりは自律性が高い
* Devinよりは自律性が低い
* 総じて**はじめてのAIエージェントとしては適している**ように感じる

※以降GitHub Copilot Agentを`Agent`と表記


---

## 開発生産性の変化

前期と今期とで、次のような違いが出ました。

* コミット数: 1,000件 → 10,000件
* PR数: 200件 → 400件
* リリース: 30件 → 50件 (※)

<span class="secondary">※リリース業務の改善による影響もあり。その改善もCopilotを利用しています</span>

---

# 大変だったこと

Agentを"使う"理由と同じくらい、**Agentを"使わない"理由もある**。

* そもそもAgentの使い方がわからない → Step1: Agentの導入
* Agentを使う場面が実は少ない → Step2: promptの整備
* Agentにも限界がある → Step3: instructionのメンテナンス

チーム単位で効果を出すには、上記のような課題をひとつずつ解決せねばならない。

以下、それぞれの解決策を紹介する。

---

# Step1: Agentの導入

---

# Step1: Agentの導入

「**利用できない**」を解消することで、後続の利用が促進される。
とくに"使いこなせる人"と"使いこなせない人"との間で**分断が起きる**ことを必ず避ける。

1. 全体的な**システムプロンプト**を準備しておく  
   ※余裕があれば、後述のpromptも用意できるとベスト
2. Agentのインストールから最初の利用までを**ハンズオンで同期的に**行う

まずは効果の有無を問わず、全員がAgentを使える状態にする。

<!--
ドキュメントによる非同期な導入も可能だが、ここで脱落する人がいるリスクは大きすぎる
-->

---

# システムプロンプトの中身

`.github/copilot-instructions.md`としてコミットする。

```.github/copilot-instructions.md
# やりとりについて

- 特定の指示がないかぎり、日本語での回答をしてください
- 特定の指示がないかぎり、一度のやりとりで行う修正は  
  ひとつのコミットレベルになる程度の規模にしてください
- 作業内容を簡潔にまとめたレポートを作成してください
    - `.github/copilot_logs/{YYYY-MM-DD}_{指示概要}.md`
```

---

# Step2: promptの整備

---

# Step2: promptの整備

メンバーによってはちょうど実装業務に従事しておらず「なのでAgentは使わない」と考える人もいる。
そのため実装作業だけでなく**開発業務**の観点でAgentを利用できる土台をつくる。

- **Issueの作成**
- **PRの作成**
- その他
  - 問い合わせ対応
  - SQL作成

上記それぞれのpromptを作成する。

---

# なぜIssue/PRの作成のpromptを優先するのか？

- もっとも**人間がサボりやすい**業務であるから
- もっとも**ちゃんとやると時間のかかる**業務であるから

仮に多少の誤りがあったとしても「Issueが作成されない」「PR本文が空欄」という事態は避けられる。
Agentに大量の文章を生成させたあとで、開発者が最初のレビュワーとして編集する流れになるとよい。

---

# refs: Issue作成プロンプト

- **注意**
  - `./.github/prompts`以下に格納すること
    - ex. `./.github/prompts/github/create-issue.md`
  - GitHubのMCPサーバが必要です

```
GitHubの操作アシスタントとして、Issueを作成してください。

以下の情報が提供、もしくはこれまでのやりとりから想定できます。
もし情報が不足している場合は、ユーザに質問して補完してください。

- Issueのタイトル
- 概要
- 現時点でわかっている開発内容
- 期待される動作
```

<!-- FIXME: ライブデモやりたい -->

---

# Step3: instructionのメンテナンス

このあたりから「◯◯◯はできるけど、△△△はできない」という声が出てくる。
そのような声に応えるため、**Agentのinstructionをメンテナンスする**。

ただしいきなり体系的に整備しようとするとハードルが高い。
まずは**上手くいかなかったときにメンテナンスする**ことをおすすめする。

※「instructionsもAgentに全部書かせればよい」という意見もあるが、発表者は否定的な立場。  
  Agentの大量出力の正しさを判定しきれず、結局コミットできないことが多かった。

---

# 結論

Agentを"使わない"理由をひとつずつ解消し、Agent利用が自然なものにする

- 不要なスキル差・分断を生まないように...
  - AIエージェントの利用は**全員で**やる
- コーディング業務の割合が多い人だけが使うようにならないように...
  - **IssueやPRの作成プロンプト**から改善していく
- Agent利用の限界を決めないように...
  - **Copilot用のドキュメント**のメンテナンスに注力する
  - 完璧を目指すのではなく、指示が上手くいかないタイミングでメンテしていく

---

# ご清聴ありがとうございました！

- 本日懇親会には出席しないため、よろしければお声がけください！
- 話したかったけれど話せなかったこと
  - Issue作成実行時に、そのIssue解決用のプロンプトをあわせて生成する
  - テーブル定義をまとめて放り込んでおくと、いろいろとメリットありました
  - 開発のスピード感や注意すべき点が変わるので、チームのルールや文化も見直したい
  - PRのレビュワーにCopilotを指定しましょう

<!-- presenter notes 

notes:

* タイマーを 25:00 でスタート
* Demo 時間: Step1 slide 2 分以内、Step2 before/after 1.5 分
* If Q\&A なし→Takeaways 再掲で調整

-->
