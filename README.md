# regret-analysis-notes

確率的バンディット、とくに UCB 系アルゴリズムのリグレット解析を学ぶためのノート集です。
目的は「定理を眺める」よりも、「どういう流れで上界を作るかを自力で再現できる」状態に持っていくことです。

## このリポジトリで扱うこと

- 確率的 $K$ 本腕バンディットの基本記法
- UCB 型アルゴリズムでよく出る証明パターン
- UCB1 の詳細証明
- 下界の基本的な考え方
- KL ダイバージェンスと change-of-measure による下界の見方
- 「良い事象」と「悪い事象」を分ける解析の型
- 学習用に流用しやすい証明テンプレート
- 手を動かして確認するための演習問題

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

下界編では、つぎの内容もあると読みやすいです。

- KL ダイバージェンス
- 対数の基本性質
- 仮説検定の直感

## 収録ノート

- 上界編
- [notes/01-証明パターン.md](notes/01-証明パターン.md): UCB 系で頻出の解析の流れを、証明の骨組みとして整理したノートです。
- [notes/02-UCB1-詳細証明.md](notes/02-UCB1-詳細証明.md): UCB1 の標準的な gap-dependent 上界を、定数込みで最後まで追うノートです。
- [notes/03-演習問題.md](notes/03-演習問題.md): 解析の流れを自分で再現するための演習問題とヒントです。
- [templates/UCB1-リグレット解析テンプレ.md](templates/UCB1-リグレット解析テンプレ.md): 自分で証明を書き直すときに使えるテンプレートです。
- 下界編
- [notes/04-下界の基礎.md](notes/04-下界の基礎.md): 下界とは何か、なぜ必要か、instance-dependent と minimax の違いを基礎から整理したノートです。
- [notes/05-KLとchange-of-measure.md](notes/05-KLとchange-of-measure.md): KL ダイバージェンスと change-of-measure を使って、なぜ $\log T / \mathrm{KL}$ 型の下界が出るのかを見るノートです。
- [notes/06-下界の演習問題.md](notes/06-下界の演習問題.md): 下界の読み方と証明の流れを自分で確認するための演習問題です。

## おすすめの読み方

1. まず上界編として [notes/01-証明パターン.md](notes/01-証明パターン.md) と [notes/02-UCB1-詳細証明.md](notes/02-UCB1-詳細証明.md) を読む
2. そのあと [templates/UCB1-リグレット解析テンプレ.md](templates/UCB1-リグレット解析テンプレ.md) と [notes/03-演習問題.md](notes/03-演習問題.md) で自分で再現する
3. 次に下界編として [notes/04-下界の基礎.md](notes/04-下界の基礎.md) を読んで、下界が何を主張するのかを掴む
4. 続いて [notes/05-KLとchange-of-measure.md](notes/05-KLとchange-of-measure.md) で、なぜ KL が出るのかを追う
5. 最後に [notes/06-下界の演習問題.md](notes/06-下界の演習問題.md) で整理する

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

## いまの到達点

このリポジトリでは、現在つぎの段階まではカバーしています。

- UCB 型解析の典型パターンを理解する
- UCB1 の標準的な gap-dependent 上界を証明する
- 下界の基本用語と典型的な結論を理解する
- KL に基づく下界の証明パターンの入口を掴む
- テンプレートを使って同型の解析を書き直す

逆に、つぎの話題はまだ未整理です。

- Lai-Robbins 下界の完全な厳密証明
- minimax 型の gap-free 上界
- minimax 下界の完全証明
- KL-UCB のような非 Hoeffding 型の解析
- 線形バンディットや contextual bandit への拡張
