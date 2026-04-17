# UCB1 のリグレット解析テンプレ（確率的 $K$ 本腕・報酬 $[0,1]$）

このテンプレは、UCB1 の証明を自分で書き直すための最小骨格です。
学習用として使うなら、各数式をそのまま写すのではなく、「どこで何を示しているか」を横にメモしながら埋めるのが有効です。

## 使い方

1. まず記法とアルゴリズムを自分のノートに書き直す
2. 「良い事象」の定義とその確率上界を自力で導く
3. 劣最適腕の回数上界を pull counting で示す
4. 最後にリグレット分解へ戻す

## 仮定

- 各腕 $i$ の報酬は $[0,1]$ に入る
- 腕 $i$ を引くたび i.i.d.、平均は $\mu_i$
- 初期化として各腕を 1 回ずつ引いておく

## 記法

- 最適腕を $i^*$、最適平均を $\mu^* = \mu_{i^*}$ とする
- ギャップを $\Delta_i = \mu^* - \mu_i$ とする
- $N_i(t)$: 時刻 $t$ までの腕 $i$ の試行回数
- $\hat{\mu}_i(s)$: 腕 $i$ を $s$ 回引いたときの経験平均

## アルゴリズム（UCB1）

各時刻 $t \geq K+1$ で

```math
I_t \in \arg\max_i
\left[
\hat{\mu}_i(N_i(t-1)) + \mathrm{rad}(N_i(t-1), t)
\right]
```

とする。ただし

```math
\mathrm{rad}(s,t) = \sqrt{\frac{2 \log t}{s}}.
```

## リグレット分解

```math
\begin{aligned}
R_T
&= \sum_{t=1}^T (\mu^* - \mu_{I_t}) \\
&= \sum_{i:\Delta_i>0} \Delta_i \,\mathbb{E}[N_i(T)].
\end{aligned}
```

したがって、各劣最適腕 $i$ について $\mathbb{E}[N_i(T)]$ を抑えれば十分です。

## 補題（Hoeffding）

任意の腕 $i$ と試行回数 $s$ について

```math
\mathbb{P}\!\left( \hat{\mu}_i(s) - \mu_i \geq \varepsilon \right)
\leq
\exp(-2 s \varepsilon^2)
```

```math
\mathbb{P}\!\left( \mu_i - \hat{\mu}_i(s) \geq \varepsilon \right)
\leq
\exp(-2 s \varepsilon^2).
```

$\varepsilon = \mathrm{rad}(s,t) = \sqrt{(2 \log t)/s}$ と置くと

```math
\mathbb{P}\!\left(
\hat{\mu}_i(s) - \mu_i \geq \mathrm{rad}(s,t)
\right)
\leq
t^{-4}
```

```math
\mathbb{P}\!\left(
\mu_i - \hat{\mu}_i(s) \geq \mathrm{rad}(s,t)
\right)
\leq
t^{-4}.
```

## 良い事象

```math
E_t
:=
\bigcap_{i=1}^K
\bigcap_{s=1}^{t}
\left\{
\left| \hat{\mu}_i(s) - \mu_i \right|
\leq
\mathrm{rad}(s,t)
\right\}.
```

合併界より

```math
\mathbb{P}(E_t^c)
\leq
\sum_{i=1}^K \sum_{s=1}^{t} 2 t^{-4}
=
2K t^{-3}.
```

したがって

```math
\sum_{t=1}^{\infty} \mathbb{P}(E_t^c) < \infty
```

となり、悪い事象の総寄与は定数です。

## 劣最適腕の回数上界（pull counting）

劣最適腕 $i$（$\Delta_i > 0$）を固定し、

```math
m_i := \left\lceil \frac{8 \log T}{\Delta_i^2} \right\rceil
```

と定義します。

主張: $E_t$ が成立し、かつ $N_i(t-1) \geq m_i$ なら、時刻 $t$ に腕 $i$ は選ばれません。

### 証明の型

もし腕 $i$ が選ばれたなら

```math
\mathrm{UCB}_i(t) \geq \mathrm{UCB}_{i^*}(t)
```

です。
$E_t$ の下で

```math
\mathrm{UCB}_i(t)
\leq
\mu_i + 2 \mathrm{rad}(N_i(t-1), t)
```

```math
\mathrm{UCB}_{i^*}(t) \geq \mu^*
```

なので

```math
\mu_i + 2 \mathrm{rad}(N_i(t-1), t) \geq \mu^*
```

すなわち

```math
2 \mathrm{rad}(N_i(t-1), t) \geq \Delta_i
```

が必要です。

一方、$N_i(t-1) \geq m_i$ かつ $t \leq T$ なら

```math
2 \mathrm{rad}(N_i(t-1), t)
\leq
2 \sqrt{\frac{2 \log T}{m_i}}
\leq
\Delta_i
```

となり矛盾です。
したがって、$m_i$ 回を超えて腕 $i$ が引かれるのは悪い事象 $E_t^c$ のときだけです。

## 期待値評価

```math
N_i(T)
\leq
m_i
+
\sum_{t=1}^T \mathbf{1}\{ I_t = i \text{ and } E_t^c \}.
```

期待値を取ると

```math
\mathbb{E}[N_i(T)]
\leq
m_i + \sum_{t=1}^T \mathbb{P}(E_t^c)
\leq
\left\lceil \frac{8 \log T}{\Delta_i^2} \right\rceil + O(1).
```

## 結論（リグレット上界）

```math
\begin{aligned}
R_T
&= \sum_{i:\Delta_i>0} \Delta_i \,\mathbb{E}[N_i(T)] \\
&\leq
\sum_{i:\Delta_i>0}
\left(
\frac{8 \log T}{\Delta_i} + O(\Delta_i)
\right).
\end{aligned}
```

## このテンプレを流用するときに差し替える場所

- 信頼半径 $\mathrm{rad}(s,t)$ の定義
- 使用する集中不等式
- 良い事象の確率上界 $\mathbb{P}(E_t^c)$
- 閾値 $m_i$ の定数
- 最後のリグレット上界の定数項

## 自分で確認すべき点

- 初期化を明示したか
- 最適腕側の下界を使ったか
- $t$ と $T$ の置き換えに無理がないか
- $\Delta_i = 0$ の腕を和から除いているか
