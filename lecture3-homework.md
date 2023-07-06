# 第3课 课后作业
# 第 3 课 练习

给定整数 $x, m$，如果 $x$ 在模 $m$ 下是二次剩余，即存在整数 $s$，使得 $s^{2} \equiv x(\bmod m)$，则记作 $QR(m, x)=1$ ; 如果 $x$ 在模 $m$ 下不是二次剩余，则记作 $QR(m, x)=0$。

<details>
<summary>英文原文</summary>

Given integers $x, m$, write $QR(m, x)=1$ if $x$ is a quadratic residue $\bmod m$, i.e., there exists an integer $s$ such that $s^{2} \equiv x(\bmod m)$; write $QR(m, x)=0$ if $x$ is not a quadratic residue $\bmod m$.

</details>

#### 二次非剩余 Quadratic nonresidue

- (a) **完备性**：如果 $QR(m, x)=0$ 并且双方都按照协议行事，那么验证者总是接受。

- (b) **可靠性**：如果 $QR(m, x)=1$，那么无论 Prover 做什么（Prover 不必遵循协议），Verifer 都会以 $\geq 1 / 2$ 的概率拒绝

#### 二次剩余 Quadratic residue

- (a) **完备性**：如果双方都按照协议行事，那么 Verifier 总是接受。

- (b) **可靠性**：如果 $QR(m, x)=0$（特别是，Prover 不知道任何有效的 $s$ ），那么无论 Prover 做什么，Verifier 都以 $\geq 1 / 2$ 的几率拒绝。

- (b\*) **知识可靠性**：对于任何使得Verifier接受的证明算法，如果允许Verifier同时查询$b\in\{0,1\}$的两个值（对于 相同的 $t$ )，则 Verifier 可以从 Prover 中提取秘密 $s$。 （这里的重点是除非证明者“知道”$s$，否则证明者无法说服验证者。）

- (c) **零知识**：无论验证者做什么（验证者的行为可能与协议不同），验证者都可以在不与证明者交互的情况下自行模拟整个交互，这样如果验证者接受，那么记录下来的信息与实际交互几乎不可区分。


#### 双线性自映射意味着DDH的失效 Self-pairing implies failure of DDH

设 $\mathbb{G}$ 和 $\mathbb{G}_{T}$ 是相同素数阶 $q$ 的阿贝尔群（以加法写出）。 设 $g \in \mathbb{G}$ 为生成元。 假设我们有一个可有效计算的非退化双线性对

$$
e: \mathbb{G} \times \mathbb{G} \rightarrow \mathbb{G}_{T}
$$

给出一个有效的决策算法，给定 $\alpha g, \beta g, y \in \mathbb{G}$（其中 $\alpha, \beta \in \mathbb{Z}_{q}$为未知)，判断是否$\alpha \beta g=y$。

提示：计算与 $g$ 的映射。

此练习表明确定性 Diffie-Hellman (DDH) 假设对于双线性自映射是错误的。

<details>
<summary>英文原文</summary>

Let $\mathbb{G}$ and $\mathbb{G}_{T}$ be abelian groups (written additively) of the same prime order $q$. Let $g \in \mathbb{G}$ be a generator. Suppose that we have an efficiently computable nondegenerate bilinear pairing

$$
e: \mathbb{G} \times \mathbb{G} \rightarrow \mathbb{G}_{T}
$$

Give an efficient algorithm for deciding, given $\alpha g, \beta g, y \in \mathbb{G}$ (with unknown $\alpha, \beta \in \mathbb{Z}_{q}$), whether $\alpha \beta g=y$.

Hint: compute the pairing against $g$.

This exercise shows that the Decisional Diffie-Hellman (DDH) assumption is false for groups with self-pairing.

</details>

#### BLS 签名聚合 BLS signature aggregation

::: tip 设置

1. 映射 Pairing $e: \mathbb{G}_{0} \times \mathbb{G}_{1} \rightarrow \mathbb{G}_{T}$，其中$\mathbb{G}_{0} , \mathbb{G}_{1}, \mathbb{G}_{T}$ 是素数阶 $p$ 的阿贝尔群（以加法写出），生成元分别是 $g_{0}, g_{1}, g_{ T}$

1. 哈希函数 $H$ 在 $\mathbb{G}_{1}$ 中取值
::::

BLS签名方案定义为：

- $\operatorname{KeyGen()}$: 选择随机（私钥）$sk=\alpha \leftarrow{ }_{R} \mathbb{Z}_{q}$ 并设置（公钥）$pk \leftarrow \alpha g_{0} \in \mathbb{G}_{0}$
- $\operatorname{Sign}(sk, m)$: 输出 $\sigma \leftarrow \alpha H(m) \in \mathbb{G}_{1}$ 作为消息 $m$ 上的签名
- $\operatorname{Verify}(pk, m, \sigma)$ ：如果 $e\left(g_{0}, \sigma\right)=e(pk, H(m))$，则接受。

检查验证算法是否始终能接受正确签名。 还争辩说，如果给伪造者的是 $pk$ 而不是 $sk$，那么为任何消息 $m$（伪造者可以自由选择 $m$ ）想出一个伪造的签名 $\sigma$ 是计算上不可行的。 这在选择的消息攻击下被称为存在不可伪造。 你能指定一些计算难度假设吗？

请检查验证算法是否总是接受正确的签名。并论证：在给定$pk$但没有$sk$的情况下，（伪造者可以选择消息）对于任何消息$m$，构造出一个伪造的签名$\sigma$是计算上不可行的 。这被称为 _在选择消息攻击下的存在性不可伪造性_ 。您能找到一些计算难度假设吗？

**签名聚合**。 给定三元组 $\left(pk_{i}, m_{i}, \sigma_{i}\right)$ 其中 $i=1, \ldots, n$（即来自 $n$ 个用户），我们可以聚合这些 $n$ 个签名 $\sigma_{1}, \ldots, \sigma_{n}$ 通过简单地在 $\mathbb{G}_{1}$ 中求和，将其合并为一个签名：

$$
\sigma \leftarrow \sigma_{1}+\cdots \sigma_{n} \in \mathbb{G}_{1}
$$

给定 $\left(pk_{1}, m_{1}\right), \ldots,\left(pk_{n}, m_{n}\right)$ 和聚合签名 $\sigma$，表明我们可以 通过检查来验证确实所有 $n$ 用户都签署了他们的消息

$$
e\left(g_{0}, \sigma\right)=e\left(pk_{1}, H\left(m_{1}\right)\right)+\cdots+e\left(pk_{n}, H\left(m_{n}\right)\right)
$$

注意：由于 _恶意公钥攻击_ ，上述签名聚合方案不安全。 使方案安全的一种方法是确保所有消息 $m_{i}$ 是不同的，例如，要求每个 $m_{i}$ 以用户的公钥 $pk_{i}$ 开头。

有关 BLS 签名聚合的更多信息，请参阅 <https://crypto.stanford.edu/~dabo/pubs/papers/BLSmultisig.html>


<details>
<summary>英文原文</summary>

::: tip SETUP

1. Pairing $e: \mathbb{G}_{0} \times \mathbb{G}_{1} \rightarrow \mathbb{G}_{T}$, where $\mathbb{G}_{0}, \mathbb{G}_{1}, \mathbb{G}_{T}$ are abelian groups (written additively) of prime order $p$, with generators $g_{0}, g_{1}, g_{T}$ respectively

1. Hash function $H$ taking values in $\mathbb{G}_{1}$
:::

The BLS signature scheme is defined as:

- $\operatorname{KeyGen()}$: choose random (secret key) $sk=\alpha \leftarrow{ }_{R} \mathbb{Z}_{q}$ and set (public key) $pk \leftarrow \alpha g_{0} \in \mathbb{G}_{0}$
- $\operatorname{Sign}(sk, m)$: output $\sigma \leftarrow \alpha H(m) \in \mathbb{G}_{1}$ as the signature on the message $m$
- $\operatorname{Verify}(pk, m, \sigma)$ : accept if $e\left(g_{0}, \sigma\right)=e(pk, H(m))$.

Check that the verification algorithm always accepts a correctly signed signature. Also argue that it is computational infeasible to come up with a forged signature $\sigma$ for any message $m$ (the forger is free to chose $m$ ) if the forger is given $pk$ but not $sk$. This is called _existentially unforgeable under a chosen message attack_. Can you specify some computational hardness assumptions?

**Signature aggregation**. Given triples $\left(pk_{i}, m_{i}, \sigma_{i}\right)$ for $i=1, \ldots, n$ (coming from $n$ users), we can aggregate these $n$ signatures $\sigma_{1}, \ldots, \sigma_{n}$ into a single signature by simply taking their sum in $\mathbb{G}_{1}$ :

$$
\sigma \leftarrow \sigma_{1}+\cdots \sigma_{n} \in \mathbb{G}_{1}
$$

Given $\left(pk_{1}, m_{1}\right), \ldots,\left(pk_{n}, m_{n}\right)$ and the aggregate signature $\sigma$, show that we can verify that indeed all $n$ users have signed their messages by checking that

$$
e\left(g_{0}, \sigma\right)=e\left(pk_{1}, H\left(m_{1}\right)\right)+\cdots+e\left(pk_{n}, H\left(m_{n}\right)\right)
$$

Note. The above signature aggregation scheme is not secure due to a _rogue public key attack_. One way to make the scheme secure is to ensure that all messages $m_{i}$ are distinct, for example, by requiring that each $m_{i}$ starts with the user's public key $pk_{i}$.

For more on BLS signature aggregation, see <https://crypto.stanford.edu/~dabo/pubs/papers/BLSmultisig.html>

</details>
