# KL と change-of-measure で見る下界

このノートでは、下界の証明で中心になる

- KL ダイバージェンス
- 対数尤度比
- change-of-measure

の三つを使って、なぜ $\log T / \mathrm{KL}$ 型の下界が現れるのかを見ます。

完全に一般的な Lai-Robbins の厳密証明までは行きませんが、証明の骨格が見えるところまでは追います。

## 目標

固定した劣最適腕 $i$ について、

```math
\mathbb{E}[N_i(T)]
```

がなぜ

```math
\text{少なくとも } \frac{\log T}{\mathrm{KL}}
```

オーダー必要になるのか、その理由を理解するのが目標です。

## 1. Bernoulli KL を確認する

報酬が Bernoulli のとき、平均 $p,q \in (0,1)$ に対応する分布の KL ダイバージェンスは

```math
d(p,q)
:=
p \log \frac{p}{q}
+
(1-p)\log\frac{1-p}{1-q}
```

です。

この量は

- $d(p,q) \geq 0$
- $d(p,q)=0$ となるのは $p=q$ のときだけ

という性質を持ちます。

つまり、$d(p,q)$ が大きいほど、Bernoulli$(p)$ と Bernoulli$(q)$ は見分けやすいと考えられます。

### 小さいギャップでの見え方

$q = p + \Delta$ で $\Delta$ が小さいとき、$d(p,q)$ はだいたい $\Delta^2$ に比例します。

正確には

```math
d(p,p+\Delta)
\approx
\frac{\Delta^2}{2p(1-p)}
```

です。

これにより、

```math
\frac{\Delta}{d(p,p+\Delta)}
```

はおおむね $1/\Delta$ オーダーになります。

これが、UCB1 の上界で出てきた

```math
\frac{\log T}{\Delta}
```

型と整合する理由です。

## 2. 「別の世界」を作る

下界では、元の問題インスタンス $\nu$ に対して、
よく似ているけれど最適腕が違う別の世界 $\nu'$ を作ります。

たとえば元の世界では

- 腕 $i^*$ が最適
- 腕 $i$ は劣最適

だとします。

ここで、腕 $i$ だけを少し良くして、

- 新しい世界 $\nu'$ では腕 $i$ が最適

になるように変更します。

すると、良いアルゴリズムは

- 世界 $\nu$ では腕 $i$ をあまり引かない
- 世界 $\nu'$ では腕 $i$ をたくさん引く

という、かなり違う振る舞いをしなければいけません。

しかし、もし $\nu$ と $\nu'$ がほとんど見分けられないなら、そのような振る舞いの切り替えは簡単ではありません。

この矛盾が下界の源です。

## 3. 対数尤度比

各腕 $j$ の元の分布を $\nu_j$、別世界での分布を $\nu'_j$ とします。

時刻 $T$ までの全履歴に対する対数尤度比を $L_T$ と書くと、概念的には

```math
L_T
=
\sum_{j=1}^K
\sum_{s=1}^{N_j(T)}
\log \frac{d\nu_j}{d\nu'_j}(X_{j,s})
```

です。

ここで重要なのは、その期待値です。
元の世界 $\nu$ の下で期待値を取ると

```math
\mathbb{E}_{\nu}[L_T]
=
\sum_{j=1}^K
\mathbb{E}_{\nu}[N_j(T)] \, \mathrm{KL}(\nu_j,\nu'_j)
```

となります。

つまり、

- ある腕をたくさん引くほど
- その腕についての KL ぶんだけ
- 二つの世界を区別する情報が増える

ということです。

これが KL が出てくる最も重要な場所です。

## 4. change-of-measure の形

下界でよく使う基本不等式は、つぎの形です。

任意の事象 $E$ に対して

```math
\sum_{j=1}^K
\mathbb{E}_{\nu}[N_j(T)] \, \mathrm{KL}(\nu_j,\nu'_j)
\geq
\mathrm{kl}\!\left(
\mathbb{P}_{\nu}(E),
\mathbb{P}_{\nu'}(E)
\right)
```

が成り立ちます。

ここで

```math
\mathrm{kl}(x,y)
:=
x \log \frac{x}{y}
+
(1-x)\log\frac{1-x}{1-y}
```

は Bernoulli パラメータ $x,y$ の KL ダイバージェンスです。

この式の意味は、

- 履歴全体から得られる情報量は左辺
- その情報を使って、ある事象 $E$ の確率をどれだけ変えられるかは右辺

ということです。

右辺が大きいということは、
二つの世界で $E$ の起こりやすさがかなり違うことを意味します。
そのためには左辺、つまり観測による総情報量も大きくなければいけません。

## 5. なぜ suboptimal arm の pull 数が下から抑えられるのか

いま、元の世界 $\nu$ では腕 $i$ が劣最適で、
別世界 $\nu'$ では腕 $i$ が最適になるように作ったとします。

このとき自然な事象は

```math
E := \{ N_i(T) > T/2 \}
```

です。

良いアルゴリズムなら、

- 元の世界 $\nu$ では、劣最適腕 $i$ を半分以上引くのはまずい
- 別世界 $\nu'$ では、最適腕 $i$ を半分以下しか引かないのはまずい

ので、

- $\mathbb{P}_{\nu}(E)$ は小さい
- $\mathbb{P}_{\nu'}(E)$ は大きい

はずです。

すると

```math
\mathrm{kl}\!\left(
\mathbb{P}_{\nu}(E),
\mathbb{P}_{\nu'}(E)
\right)
```

は大きくなります。

その結果、左辺も大きくならなければいけません。

## 6. 腕を一つだけ変えると何が起きるか

さらに、$\nu$ と $\nu'$ の違いを腕 $i$ だけにしたとします。
つまり

```math
\nu_j = \nu'_j \qquad (j \neq i)
```

です。

すると左辺は

```math
\mathbb{E}_{\nu}[N_i(T)] \, \mathrm{KL}(\nu_i,\nu'_i)
```

だけになります。

したがって

```math
\mathbb{E}_{\nu}[N_i(T)] \, \mathrm{KL}(\nu_i,\nu'_i)
\geq
\mathrm{kl}\!\left(
\mathbb{P}_{\nu}(E),
\mathbb{P}_{\nu'}(E)
\right)
```

です。

右辺が $\log T$ オーダーになるなら、

```math
\mathbb{E}_{\nu}[N_i(T)]
```

は少なくとも

```math
\frac{\log T}{\mathrm{KL}(\nu_i,\nu'_i)}
```

オーダー必要になります。

これが、suboptimal arm の試行回数に対する下界です。

## 7. なぜ右辺が $\log T$ オーダーになるのか

ここでは厳密証明ではなく、直感を押さえます。

良いアルゴリズムの条件として、たとえば

```math
\mathbb{E}_{\nu}[R_T] = o(T^a)
\qquad
\text{for every } a>0
```

のような「uniformly good」な性質を仮定します。

すると、

- 元の世界では劣最適腕を何度も引きすぎることはできない
- 別世界では最適腕をほとんど引かないこともできない

ので、

- $\mathbb{P}_{\nu}(E)$ は 0 に近づく
- $\mathbb{P}_{\nu'}(E)$ は 1 に近づく

と期待されます。

このとき二値 KL

```math
\mathrm{kl}(p,q)
```

は、$p$ が非常に小さく $q$ が 1 に近いとき、おおむね $\log T$ オーダーまで増えます。

そのため

```math
\mathbb{E}_{\nu}[N_i(T)] \, \mathrm{KL}(\nu_i,\nu'_i)
```

が $\log T$ オーダー必要になります。

## 8. Bernoulli bandit での見え方

Bernoulli のとき、腕 $i$ の平均を $\mu_i$、最適平均を $\mu^*$ とすると、
最終的には

```math
\liminf_{T \to \infty}
\frac{\mathbb{E}_{\nu}[N_i(T)]}{\log T}
\geq
\frac{1}{d(\mu_i,\mu^*)}
```

という形が現れます。

したがってリグレットについては

```math
\liminf_{T \to \infty}
\frac{\mathbb{E}_{\nu}[R_T]}{\log T}
\geq
\sum_{i:\Delta_i>0}
\frac{\Delta_i}{d(\mu_i,\mu^*)}
```

です。

これは Bernoulli bandit における Lai-Robbins 型下界の形です。

## 9. 上界との対応

UCB1 の上界は

```math
\sum_{i:\Delta_i>0} \frac{\log T}{\Delta_i}
```

型でした。

一方、下界は

```math
\sum_{i:\Delta_i>0} \frac{\Delta_i \log T}{d(\mu_i,\mu^*)}
```

型です。

$d(\mu_i,\mu^*)$ は小さいギャップでは $\Delta_i^2$ に比例するので、
下界もやはり

```math
\sum_{i:\Delta_i>0} \frac{\log T}{\Delta_i}
```

オーダーになります。

つまり、UCB1 の $\log T$ と $1/\Delta_i$ は偶然ではなく、
問題自体の本質的な難しさにかなり近いということです。

## 10. このノートで掴んでほしい骨格

下界の証明は、細かい技術は違っても、骨格としてはつぎの流れです。

1. 元の世界と、最適腕が違う別世界を作る
2. 両世界でアルゴリズムが違う振る舞いをしなければ困る事象 $E$ を作る
3. change-of-measure で、その違いを作るには十分な情報量が必要だと示す
4. 情報量は pull 数と KL の積で表される
5. したがって pull 数は少なくとも $\log T / \mathrm{KL}$ 必要になる

この五段階が見えていれば、下界の論文や講義ノートもかなり読みやすくなります。

## 11. 次に進むなら

次の読み方は二通りあります。

### まず厳密証明を読みたいなら

- [07-Lai-Robbins-厳密証明.md](07-Lai-Robbins-厳密証明.md)
- [08-minimax-下界-厳密証明.md](08-minimax-下界-厳密証明.md)

を読むのが自然です。

### 先に定着を優先するなら

[06-下界の演習問題.md](06-下界の演習問題.md) で、

- instance-dependent と minimax の違い
- KL が出る場所
- $\log T$ が出る理由

を自分の言葉で説明できるか確認するのがおすすめです。
