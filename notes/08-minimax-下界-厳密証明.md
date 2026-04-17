# minimax 下界の厳密証明

このノートでは、Bernoulli bandit に対する minimax 下界

```math
\inf_A \sup_{\nu} \mathbb{E}_{\nu}[R_T(A)]
\geq
c \sqrt{KT}
```

を厳密に証明します。

ここで

- $A$ は任意のアルゴリズム
- $\nu$ は任意の bandit instance
- $c>0$ は universal constant

です。

証明は、よく似た環境を $K$ 個並べて、それらを一つのアルゴリズムが同時にうまく扱うのは無理だと示すものです。

## 定理

### 定理

任意のアルゴリズム $A$、任意の $K \geq 2$、任意の $T \geq K$ に対して、
ある $K$ 本腕 Bernoulli bandit instance $\nu$ が存在して

```math
\mathbb{E}_{\nu}[R_T(A)]
\geq
\frac{1}{32}\sqrt{KT}
```

が成り立つ。

したがって

```math
\inf_A \sup_{\nu} \mathbb{E}_{\nu}[R_T(A)]
\geq
\frac{1}{32}\sqrt{KT}.
```

## 環境族

パラメータ

```math
\varepsilon := \frac{1}{8}\sqrt{\frac{K}{T}}
```

を取ります。

$T \geq K$ なので $\varepsilon \leq 1/8 < 1/4$ です。

つぎの $K+1$ 個の環境を考えます。

### 基準環境 $\nu^0$

すべての腕が Bernoulli$(1/2)$。

### 変形環境 $\nu^i$ （$i=1,\dots,K$）

- 腕 $i$ だけ Bernoulli$(1/2+\varepsilon)$
- 他の腕はすべて Bernoulli$(1/2)$

この環境では、腕 $i$ が一意の最適腕で、各劣最適腕とのギャップは $\varepsilon$ です。

以後、

- $\mathbb{P}_i, \mathbb{E}_i$ を環境 $\nu^i$ における確率・期待値
- $P_i^T$ を時刻 $T$ までの履歴分布

と書きます。

## 補題 1. 各環境での regret の形

環境 $\nu^i$ では、最適腕は腕 $i$ なので

```math
R_T
=
\varepsilon \sum_{j \neq i} N_j(T)
=
\varepsilon (T - N_i(T)).
```

したがって

```math
\mathbb{E}_i[R_T]
=
\varepsilon \left( T - \mathbb{E}_i[N_i(T)] \right).
```

これは定義から直ちに従います。

## 補題 2. TV による期待値差の評価

任意の二つの確率分布 $P,Q$ と、任意の可測関数 $f$ で

```math
0 \leq f \leq T
```

ならば

```math
\mathbb{E}_P[f]
\leq
\mathbb{E}_Q[f] + T \, \mathrm{TV}(P,Q)
```

が成り立ちます。

ここで $\mathrm{TV}$ は total variation distance です。

### 証明

期待値差の一般不等式

```math
\left| \mathbb{E}_P[f] - \mathbb{E}_Q[f] \right|
\leq
\|f\|_{\infty}\,\mathrm{TV}(P,Q)
```

を使えばよいです。
$\|f\|_{\infty} \leq T$ なので結論が従います。

証明終わり。

## 補題 3. 基準環境との KL

各 $i=1,\dots,K$ について

```math
\mathrm{KL}(P_0^T,P_i^T)
=
\mathbb{E}_0[N_i(T)] \, d\!\left(\frac{1}{2}, \frac{1}{2}+\varepsilon\right)
```

が成り立ちます。

### 証明

KL の chain rule より

```math
\mathrm{KL}(P_0^T,P_i^T)
=
\sum_{t=1}^T
\mathbb{E}_0
\left[
\mathrm{KL}
\left(
\mathcal{L}_0(X_t \mid H_{t-1}, I_t),
\mathcal{L}_i(X_t \mid H_{t-1}, I_t)
\right)
\right]
```

です。

基準環境 $\nu^0$ と変形環境 $\nu^i$ の違いは腕 $i$ の reward 分布だけです。

- $I_t \neq i$ なら条件付き KL は 0
- $I_t = i$ なら条件付き KL は $d(1/2,1/2+\varepsilon)$

なので

```math
\mathrm{KL}(P_0^T,P_i^T)
=
\sum_{t=1}^T
\mathbb{E}_0[\mathbf{1}\{I_t=i\}]
\,
d\!\left(\frac{1}{2}, \frac{1}{2}+\varepsilon\right)
```

です。したがって

```math
\mathrm{KL}(P_0^T,P_i^T)
=
\mathbb{E}_0[N_i(T)] \, d\!\left(\frac{1}{2}, \frac{1}{2}+\varepsilon\right).
```

証明終わり。

## 補題 4. Bernoulli KL の評価

$0 < \varepsilon \leq 1/4$ のとき

```math
d\!\left(\frac{1}{2}, \frac{1}{2}+\varepsilon\right)
\leq
4\varepsilon^2
```

が成り立ちます。

### 証明

直接計算すると

```math
d\!\left(\frac{1}{2}, \frac{1}{2}+\varepsilon\right)
=
\frac{1}{2}\log\frac{1}{1-4\varepsilon^2}.
```

$x \in [0,1/2]$ に対して

```math
-\log(1-x) \leq 2x
```

なので、$x=4\varepsilon^2 \leq 1/4$ を代入して

```math
d\!\left(\frac{1}{2}, \frac{1}{2}+\varepsilon\right)
\leq
\frac{1}{2} \cdot 2 \cdot 4\varepsilon^2
=
4\varepsilon^2.
```

証明終わり。

## 補題 5. 各環境での最適腕 pull 数の上界

各 $i=1,\dots,K$ について

```math
\mathbb{E}_i[N_i(T)]
\leq
\mathbb{E}_0[N_i(T)]
+
T\varepsilon \sqrt{2\mathbb{E}_0[N_i(T)]}.
```

### 証明

補題 2 を

```math
f(H_T) := N_i(T)
```

に適用すると

```math
\mathbb{E}_i[N_i(T)]
\leq
\mathbb{E}_0[N_i(T)] + T\,\mathrm{TV}(P_i^T,P_0^T).
```

Pinsker の不等式より

```math
\mathrm{TV}(P_i^T,P_0^T)
\leq
\sqrt{\frac{\mathrm{KL}(P_0^T,P_i^T)}{2}}.
```

補題 3 と補題 4 から

```math
\mathrm{KL}(P_0^T,P_i^T)
\leq
4\varepsilon^2 \mathbb{E}_0[N_i(T)].
```

したがって

```math
\mathrm{TV}(P_i^T,P_0^T)
\leq
\varepsilon \sqrt{2\mathbb{E}_0[N_i(T)]}.
```

これを代入して

```math
\mathbb{E}_i[N_i(T)]
\leq
\mathbb{E}_0[N_i(T)]
+
T\varepsilon \sqrt{2\mathbb{E}_0[N_i(T)]}.
```

証明終わり。

## 定理の証明

まず、補題 1 より

```math
\frac{1}{K}\sum_{i=1}^K \mathbb{E}_i[R_T]
=
\varepsilon T
-
\frac{\varepsilon}{K}\sum_{i=1}^K \mathbb{E}_i[N_i(T)].
```

そこで、$\sum_i \mathbb{E}_i[N_i(T)]$ を上から抑えます。

補題 5 を各 $i$ について足し合わせると

```math
\sum_{i=1}^K \mathbb{E}_i[N_i(T)]
\leq
\sum_{i=1}^K \mathbb{E}_0[N_i(T)]
+
T\varepsilon\sqrt{2}
\sum_{i=1}^K \sqrt{\mathbb{E}_0[N_i(T)]}.
```

基準環境では各時刻に必ず 1 本の腕だけを引くので

```math
\sum_{i=1}^K \mathbb{E}_0[N_i(T)] = T.
```

また Cauchy-Schwarz より

```math
\sum_{i=1}^K \sqrt{\mathbb{E}_0[N_i(T)]}
\leq
\sqrt{
K \sum_{i=1}^K \mathbb{E}_0[N_i(T)]
}
=
\sqrt{KT}.
```

したがって

```math
\sum_{i=1}^K \mathbb{E}_i[N_i(T)]
\leq
T + T\varepsilon\sqrt{2KT}.
```

これを平均 regret の式に戻すと

```math
\frac{1}{K}\sum_{i=1}^K \mathbb{E}_i[R_T]
\geq
\varepsilon T \left(1-\frac{1}{K}\right)
-
\frac{\varepsilon^2 T \sqrt{2KT}}{K}.
```

ここで

```math
\varepsilon = \frac{1}{8}\sqrt{\frac{K}{T}}
```

を代入すると

```math
\varepsilon T \left(1-\frac{1}{K}\right)
=
\frac{1}{8}\sqrt{KT}\left(1-\frac{1}{K}\right)
\geq
\frac{1}{16}\sqrt{KT}
```

です。最後の不等式では $K \geq 2$ を使いました。

また

```math
\frac{\varepsilon^2 T \sqrt{2KT}}{K}
=
\frac{1}{64}\sqrt{2KT}.
```

したがって

```math
\frac{1}{K}\sum_{i=1}^K \mathbb{E}_i[R_T]
\geq
\left(
\frac{1}{16} - \frac{\sqrt{2}}{64}
\right)\sqrt{KT}.
```

右辺の係数は $1/32$ より大きいので

```math
\frac{1}{K}\sum_{i=1}^K \mathbb{E}_i[R_T]
\geq
\frac{1}{32}\sqrt{KT}.
```

平均がこれだけ大きいので、少なくとも一つの $i \in \{1,\dots,K\}$ について

```math
\mathbb{E}_i[R_T]
\geq
\frac{1}{32}\sqrt{KT}
```

が成り立ちます。

すなわち、任意のアルゴリズムに対して、そのアルゴリズムを少なくとも $\frac{1}{32}\sqrt{KT}$ だけ後悔させる環境が存在します。

したがって

```math
\sup_{\nu} \mathbb{E}_{\nu}[R_T(A)]
\geq
\frac{1}{32}\sqrt{KT}
```

であり、さらに任意のアルゴリズム $A$ について成り立つので

```math
\inf_A \sup_{\nu} \mathbb{E}_{\nu}[R_T(A)]
\geq
\frac{1}{32}\sqrt{KT}.
```

定理の証明終わり。

## この証明の本質

Lai-Robbins 型下界が

- 固定した instance に対する
- $\log T$ オーダーの
- instance-dependent 下界

だったのに対し、
この minimax 下界は

- 環境をこちらで複数用意し
- どれにも同時に強いアルゴリズムは存在しない

ことから、worst-case で $\sqrt{KT}$ を強制します。

本質はつぎの三点です。

1. 各環境 $\nu^i$ は、腕 $i$ だけが少し良い
2. 基準環境 $\nu^0$ から見ると、それらはどれも非常によく似ている
3. よく似た $K$ 個の環境すべてに対して最適腕を見抜くには、時間 $T$ が足りない

## Lai-Robbins 下界との違い

Lai-Robbins 下界は、固定した instance に対して

```math
\log T
```

オーダーの探索が unavoidable だと言っていました。

一方、minimax 下界は worst-case では

```math
\sqrt{KT}
```

オーダーの regret 自体が unavoidable だと言います。

両者は矛盾していません。

- instance-dependent 下界は「固定した問題の難しさ」
- minimax 下界は「最悪の問題の難しさ」

を測っているからです。
