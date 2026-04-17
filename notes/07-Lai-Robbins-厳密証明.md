# Lai-Robbins 型下界の厳密証明

このノートでは、Bernoulli bandit に対する Lai-Robbins 型下界を、uniformly good algorithm の仮定の下で厳密に証明します。

完全に一般の分布族ではなく Bernoulli に限定しますが、

- change-of-measure
- KL ダイバージェンス
- suboptimal arm の pull 数の下界

という骨格は、そのまま一般形の証明につながります。

## 設定

- 腕は $1, \dots, K$
- 腕 $j$ の報酬分布は Bernoulli$(\mu_j)$
- 各 $\mu_j$ は $(0,1)$ に入る
- 最適腕は一意で、$i^*$ と書く
- 最適平均を $\mu^* := \mu_{i^*}$
- 劣最適腕 $i$ のギャップを $\Delta_i := \mu^* - \mu_i > 0$

時刻 $T$ までに腕 $i$ を引いた回数を $N_i(T)$、
累積リグレットを

```math
R_T
:=
\sum_{t=1}^T (\mu^* - \mu_{I_t})
=
\sum_{i:\Delta_i>0} \Delta_i \, N_i(T)
```

と書きます。

## uniformly good algorithm

アルゴリズム $A$ が uniformly good であるとは、
任意の Bernoulli bandit instance $\nu$ と任意の $a>0$ に対して

```math
\mathbb{E}_{\nu}[R_T(A)] = o(T^a)
```

が成り立つこととします。

この仮定は、

- どのインスタンスでも
- 多項式オーダーよりかなり小さい regret しか許さない

という意味です。

Lai-Robbins 型下界は、このクラスのアルゴリズムに対して成り立ちます。

## 目標定理

### 定理

$\nu = (\mu_1,\dots,\mu_K)$ を上のような Bernoulli instance とし、
$A$ を uniformly good algorithm とする。

このとき、任意の劣最適腕 $i \neq i^*$ に対して

```math
\liminf_{T \to \infty}
\frac{\mathbb{E}_{\nu}[N_i(T)]}{\log T}
\geq
\frac{1}{d(\mu_i,\mu^*)}
```

が成り立つ。

ここで

```math
d(p,q)
:=
p \log \frac{p}{q}
+
(1-p)\log\frac{1-p}{1-q}
```

は Bernoulli KL ダイバージェンスである。

したがって

```math
\liminf_{T \to \infty}
\frac{\mathbb{E}_{\nu}[R_T]}{\log T}
\geq
\sum_{i:\Delta_i>0}
\frac{\Delta_i}{d(\mu_i,\mu^*)}
```

が成り立つ。

## 証明の見取り図

証明はつぎの流れです。

1. 劣最適腕 $i$ を少しだけ良くして、別世界 $\nu^{(i,\lambda)}$ を作る
2. その二つの世界を区別するには、腕 $i$ を十分多く引く必要があると示す
3. その「十分多く」は KL を通じて $\log T / d(\mu_i,\lambda)$ になる
4. 最後に $\lambda \downarrow \mu^*$ として下界を得る

## 代替インスタンス

劣最適腕 $i \neq i^*$ を一つ固定し、
任意の $\lambda \in (\mu^*,1)$ に対して

```math
\nu^{(i,\lambda)}
:=
(\mu_1,\dots,\mu_{i-1},\lambda,\mu_{i+1},\dots,\mu_K)
```

を定めます。

この新しい instance では、腕 $i$ が一意の最適腕になります。

以後、

- 元の世界での確率・期待値を $\mathbb{P}_{\nu}, \mathbb{E}_{\nu}$
- 別世界での確率・期待値を $\mathbb{P}_{\lambda}, \mathbb{E}_{\lambda}$

と書きます。

## 補題 1. 履歴全体の KL

時刻 $T$ までの履歴を $H_T$ とし、
その分布を元の世界と別世界でそれぞれ $P_{\nu}^T, P_{\lambda}^T$ と書きます。

### 主張

```math
\mathrm{KL}(P_{\nu}^T, P_{\lambda}^T)
=
\mathbb{E}_{\nu}[N_i(T)] \, d(\mu_i,\lambda)
```

### 証明

KL の chain rule より

```math
\mathrm{KL}(P_{\nu}^T, P_{\lambda}^T)
=
\sum_{t=1}^T
\mathbb{E}_{\nu}
\left[
\mathrm{KL}
\left(
\mathcal{L}_{\nu}(X_t \mid H_{t-1}, I_t),
\mathcal{L}_{\lambda}(X_t \mid H_{t-1}, I_t)
\right)
\right]
```

です。

アルゴリズムの action selection rule 自体は環境では変わらないので、
違いが出るのは reward の条件付き分布だけです。

時刻 $t$ に $I_t \neq i$ なら、両世界で reward 分布は同じなので条件付き KL は 0 です。
時刻 $t$ に $I_t = i$ なら、条件付き KL は

```math
d(\mu_i,\lambda)
```

です。

したがって

```math
\mathrm{KL}(P_{\nu}^T, P_{\lambda}^T)
=
\sum_{t=1}^T
\mathbb{E}_{\nu}[\mathbf{1}\{I_t=i\}] \, d(\mu_i,\lambda)
=
\mathbb{E}_{\nu}[N_i(T)] \, d(\mu_i,\lambda).
```

証明終わり。

## 補題 2. 事象への data-processing

任意の事象 $E \in \sigma(H_T)$ に対して

```math
\mathrm{KL}(P_{\nu}^T, P_{\lambda}^T)
\geq
\mathrm{kl}\!\left(
\mathbb{P}_{\nu}(E),
\mathbb{P}_{\lambda}(E)
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

は二値 KL です。

### 証明

写像 $H_T \mapsto \mathbf{1}_E(H_T)$ に data-processing inequality を適用すればよいです。
この写像の下で $P_{\nu}^T, P_{\lambda}^T$ はそれぞれ

- Bernoulli$(\mathbb{P}_{\nu}(E))$
- Bernoulli$(\mathbb{P}_{\lambda}(E))$

に写るので、結論が得られます。

証明終わり。

## 補題 3. 二値 KL の粗い下界

任意の $x,y \in (0,1)$ に対して

```math
\mathrm{kl}(x,y)
\geq
x \log \frac{1}{y} - \log 2
```

が成り立ちます。

### 証明

定義より

```math
\mathrm{kl}(x,y)
=
x \log \frac{x}{y}
+
(1-x)\log\frac{1-x}{1-y}.
```

右辺第二項の $-\log(1-y)$ は非負なので捨てて

```math
\mathrm{kl}(x,y)
\geq
x \log \frac{1}{y}
+
x \log x
+
(1-x)\log(1-x).
```

ここで binary entropy の上界

```math
-x\log x - (1-x)\log(1-x) \leq \log 2
```

を使えば

```math
\mathrm{kl}(x,y)
\geq
x \log \frac{1}{y} - \log 2.
```

証明終わり。

## 定理の証明

劣最適腕 $i$ と $\lambda \in (\mu^*,1)$ を固定します。

事象

```math
E_T := \left\{ N_i(T) \leq \frac{T}{2} \right\}
```

を考えます。

### ステップ 1. 元の世界では $E_T$ が高確率

元の世界では腕 $i$ は劣最適なので

```math
R_T \geq \Delta_i N_i(T).
```

uniformly good より、任意の $a>0$ に対して

```math
\mathbb{E}_{\nu}[N_i(T)]
\leq
\frac{\mathbb{E}_{\nu}[R_T]}{\Delta_i}
=
o(T^a).
```

したがって Markov 不等式より

```math
\mathbb{P}_{\nu}(E_T^c)
=
\mathbb{P}_{\nu}\!\left( N_i(T) > \frac{T}{2} \right)
\leq
\frac{2\mathbb{E}_{\nu}[N_i(T)]}{T}
=
o(T^{a-1}).
```

とくに、任意の $\beta \in (0,1)$ について $a=1-\beta$ を選べば

```math
\mathbb{P}_{\nu}(E_T^c) = o(T^{-\beta}).
```

よって

```math
\mathbb{P}_{\nu}(E_T) \to 1.
```

### ステップ 2. 別世界では $E_T$ が低確率

別世界では腕 $i$ が最適で、他の任意の腕 $j \neq i$ はギャップ

```math
\lambda - \mu_j \geq \lambda - \mu^* > 0
```

を持ちます。

よって

```math
R_T
\geq
(\lambda - \mu^*) (T - N_i(T)).
```

uniformly good より、任意の $a>0$ に対して

```math
\mathbb{E}_{\lambda}[T-N_i(T)]
\leq
\frac{\mathbb{E}_{\lambda}[R_T]}{\lambda-\mu^*}
=
o(T^a).
```

したがって

```math
\mathbb{P}_{\lambda}(E_T)
=
\mathbb{P}_{\lambda}\!\left( T-N_i(T) \geq \frac{T}{2} \right)
\leq
\frac{2\mathbb{E}_{\lambda}[T-N_i(T)]}{T}
=
o(T^{a-1}).
```

再び任意の $\beta \in (0,1)$ に対して $a=1-\beta$ を選べば

```math
\mathbb{P}_{\lambda}(E_T) = o(T^{-\beta}).
```

### ステップ 3. change-of-measure

補題 1 と補題 2 を組み合わせると

```math
\mathbb{E}_{\nu}[N_i(T)] \, d(\mu_i,\lambda)
\geq
\mathrm{kl}\!\left(
\mathbb{P}_{\nu}(E_T),
\mathbb{P}_{\lambda}(E_T)
\right).
```

ここで

- $p_T := \mathbb{P}_{\nu}(E_T)$
- $q_T := \mathbb{P}_{\lambda}(E_T)$

とおくと、補題 3 より

```math
\mathrm{kl}(p_T,q_T)
\geq
p_T \log \frac{1}{q_T} - \log 2.
```

任意の $\beta \in (0,1)$ に対し、$q_T = o(T^{-\beta})$ なので、十分大きい $T$ では

```math
q_T \leq T^{-\beta}
```

です。したがって

```math
\log \frac{1}{q_T} \geq \beta \log T.
```

また $p_T \to 1$ なので

```math
\liminf_{T \to \infty}
\frac{\mathrm{kl}(p_T,q_T)}{\log T}
\geq
\beta.
```

$\beta \in (0,1)$ は任意だったので

```math
\liminf_{T \to \infty}
\frac{\mathrm{kl}(p_T,q_T)}{\log T}
\geq
1.
```

よって

```math
\liminf_{T \to \infty}
\frac{\mathbb{E}_{\nu}[N_i(T)]}{\log T}
\geq
\frac{1}{d(\mu_i,\lambda)}.
```

### ステップ 4. $\lambda \downarrow \mu^*$ とする

上の不等式は任意の $\lambda \in (\mu^*,1)$ に対して成り立ちます。

Bernoulli KL は第二引数について連続なので、
$\lambda \downarrow \mu^*$ とすると

```math
\liminf_{T \to \infty}
\frac{\mathbb{E}_{\nu}[N_i(T)]}{\log T}
\geq
\frac{1}{d(\mu_i,\mu^*)}.
```

これで arm-wise な下界が示されました。

### ステップ 5. リグレット下界

リグレット分解

```math
\mathbb{E}_{\nu}[R_T]
=
\sum_{i:\Delta_i>0} \Delta_i \, \mathbb{E}_{\nu}[N_i(T)]
```

と、有限個の非負項に対する

```math
\liminf_{T \to \infty} \sum_i a_{i,T}
\geq
\sum_i \liminf_{T \to \infty} a_{i,T}
```

を使うと

```math
\liminf_{T \to \infty}
\frac{\mathbb{E}_{\nu}[R_T]}{\log T}
\geq
\sum_{i:\Delta_i>0}
\frac{\Delta_i}{d(\mu_i,\mu^*)}.
```

定理の証明終わり。

## 何が本質だったのか

この証明の本質はつぎの三点です。

1. 劣最適腕を少しだけ良くした別世界を作る
2. 両世界で起こり方が大きく違う事象 $E_T$ を作る
3. その違いを作るには、腕 $i$ を少なくとも $\log T / d(\mu_i,\mu^*)$ 回引く必要がある

つまり、下界は「探索しないと世界が区別できない」という情報量の主張になっています。

## 一般形との関係

一般の Lai-Robbins 下界では、Bernoulli 特有の

```math
d(\mu_i,\mu^*)
```

の代わりに、分布族上の KL 情報量

```math
\inf_{\theta : \mu(\theta) > \mu^*}
\mathrm{KL}(\nu_i,\nu_{\theta})
```

が現れます。

このノートの証明は、その一般形の最も見通しのよい特別ケースです。
