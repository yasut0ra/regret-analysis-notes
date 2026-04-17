# regret-analysis-notes

確率的バンディット、とくに UCB 系アルゴリズムのリグレット解析を学ぶためのノート集です。
目的は「定理を眺める」よりも、「どういう流れで上界を作るかを自力で再現できる」状態に持っていくことです。

## このリポジトリで扱うこと

- 確率的 $K$ 本腕バンディットの基本記法
- UCB 型アルゴリズムでよく出る証明パターン
- 「良い事象」と「悪い事象」を分ける解析の型
- 学習用に流用しやすい証明テンプレート

## 想定している読者

- バンディットの初学者
- リグレット解析を式変形込みで追いたい人
- 論文や講義ノートの証明を読む前に、典型パターンを掴みたい人

## 前提知識

最低限、次を知っていると読みやすいです。

- 期待値、独立性、確率変数
- Hoeffding 不等式
- 合併界（union bound）
- 漸近記法 $O(\cdot)$

## 収録ノート

- [notes/01-証明パターン.md](notes/01-証明パターン.md): UCB 系で頻出の解析の流れを、証明の骨組みとして整理したノートです。
- [templates/UCB1-リグレット解析テンプレ.md](templates/UCB1-リグレット解析テンプレ.md): 自分で証明を書き直すときに使えるテンプレートです。

## おすすめの読み方

1. まず [notes/01-証明パターン.md](notes/01-証明パターン.md) を読んで、全体の型を掴む
2. 次に [templates/UCB1-リグレット解析テンプレ.md](templates/UCB1-リグレット解析テンプレ.md) を使って、自分で証明を書き直す
3. 半径の定義や集中不等式を差し替えて、他の UCB 系手法にも流用する

## 数式表示について

GitHub 上で数式が崩れないように、Markdown 内の数式は GitHub 公式の数式記法に合わせて書いています。

- 行内数式は `$...$`
- ブロック数式は ````math` のコードブロック

参考: GitHub Docs の「Writing mathematical expressions」
https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/writing-mathematical-expressions

## 今後追加したい内容

- ETC / UCB / KL-UCB の比較
- gap-dependent と gap-free の見分け方
- Thompson Sampling の解析メモ
- 線形バンディットでの自己正規化不等式の入口
