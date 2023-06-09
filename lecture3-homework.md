## 第3课 课后作业

## 第 3 课 练习

给定整数 $x, m$，如果 $x$ 在模 $m$ 下是二次剩余，即存在整数 $s$，使得 $s^{2} \equiv x(\bmod m)$，则记作 $QR(m, x)=1$ ; 如果 $x$ 在模 $m$ 下不是二次剩余，则记作 $QR(m, x)=0$。

<details>
<summary>英文原文</summary>


Given integers $x, m$, write $QR(m, x)=1$ if $x$ is a quadratic residue $\bmod m$, i.e., there exists an integer $s$ such that $s^{2} \equiv x(\bmod m)$; write $QR(m, x)=0$ if $x$ is not a quadratic residue $\bmod m$.

</details>



### 二次非剩余 Quadratic nonresidue


ASSUMPTION: The Prover can compute $QR(m, x)$ for all $m, x$

COMMON INPUT: positive integers $m, x$

GOAL: the Prover **wants to convince the Verifer that $QR(m, x) = 0$**

PROTOCOL: 

1. The Verifier picks a random $s \in Z_m$ uniformly among elements relatively prime to $m$, and also tosses a coin $b \leftarrow_R\{0,1\}$.  Set


$$
\begin{aligned}
y \leftarrow \begin{cases}s^2 x & \text { if } b=0 \\ s^2 & \text { if } b=1 \end{cases}
\end{aligned}
$$

​		The Verifier sends  $y$  to the Prover and challenges the Prover to determine  $b$.

2. The Prover computes  $QR(m, y)$  and sends its value back to the Verifier
3. The Verifier accepts if the value it received from the Prover is indeed equal to $b$;  otherwise it rejects.



Question: You are asked to check:


- (a) **完备性**：如果 $QR(m, x)=0$ 并且双方都按照协议行事，那么验证者总是接受。

- (b) **可靠性**：如果 $QR(m, x)=1$，那么无论 Prover 做什么（Prover 不必遵循协议），Verifer 都会以 $\geq 1 / 2$ 的概率拒绝



----

关于 Completeness 与 Soundness

-   **Completeness:** If the prover indeed knows how to change the state from _A_ to _B_ in a valid way then the prover will manage to convince the verifier to accept the change. (真的假不了)
-   **Soundness:** If the prover doesn’t know how to change the state from _A_ to _B_, then the verifier will notice an inconsistency (前后矛盾) in the interaction and reject the suggested state transition. There remains a tiny false-positive probability(假阳性概率) ,   i.e., a probability of the verifier accepting an invalid proof. This probability is a system security parameter which can be set to an acceptable level like  $1/(2^{128})$



-----



#### Solution

> 待验证： (a) **完备性**：如果 $QR(m, x)=0$ 并且双方都按照协议行事，那么验证者总是接受。

满足完备性:  由于 $QR(m, x) = 0$，且已知 $x$ 是模 $m$ 下的二次非剩余。接下来，我们需要根据 Verifier 掷出的随机比特 $b$ 考虑两种情况：

- 如果 $b = 0$，那么 $y = s^2x$，并且由于 $x$ 是二次非剩余，$y$ 也是二次非剩余。所以 $QR(m, y) = 0$，这与 Verifier 所预期的 $b = 0$ 相符。
- 如果 $b = 1$，那么 $y = s^2$，$y$ 是二次剩余。所以 $QR(m, y) = 1$，这与 Verifier 所预期的 $b = 1$ 相符。

因此，无论 $b$ 的值如何，Verifier 都会接受。

即： **真的假不了**



> 待验证： (b) **可靠性**：如果 $QR(m, x)=1$，那么无论 Prover 做什么（Prover 不必遵循协议），Verifer 都会以 $\geq 1 / 2$ 的概率拒绝

满足可靠性：如果 Prover 试图错误地证明 $x$ 是模 $m$ 下的二次非剩余（即 $QR(m, x) = 0$），而实际上 $x$ 是模 $m$ 下的二次剩余（即 $QR(m, x) = 1$），那么无论 Prover 做什么，Verifier 都应该有至少一半的概率拒绝 Prover。

在本例中，当 $QR(m, x) = 1$ 时，即 $x$ 是模 $m$ 下的二次剩余，两种情况：

- 当 $b = 0$  时，由于计算出的 $y = s^2x$ 也是二次剩余，而 Verifier 期望的 $b$ 值是 0，所以 Verifier 会拒绝。
- 当 $b = 1$  时，由于 $y = s^2$ 是二次剩余，符合 Verifier 期望的 $b = 1$，所以 Verifier 会接受。



### 二次剩余 Quadratic residue

COMMON INPUT: positive integers  $m, x$  with $gcd(m, x) =1$

PRIVATE INPUT: the Prover knows a secret integer $s$ such that $s^2 ≡ x$ mod m

GOAL: the Prover wants to convince the Verifier that $QR(m, x) = 1$ without revealing any information about  $s$

(Note that the Prover can easily convince the Verifier that  $QR(m, x) = 1$  by sending s to the Verifier, but this would reveal $s$  completely.)



PROTOCOL：

1. The Prover picks a random  $t \in \mathbb{Z}_m$  uniform among residues relatively prime to $m$  （与 $m$  互质） .  The Prover sends $y ← xt^2 \mod{m}$   to the Verifier.
2. The Verifier flips a coin  $b ←_{R} \{0,1\}$ and sends $b$ to the Prover.
3. The Prover receives  $b$  and sends to the Verifier the value of

$$
\begin{aligned}
u \leftarrow \begin{cases} t& \text { if } b=0 \\ st & \text { if } b=1 \end{cases}
\end{aligned}
$$

4. The Verifier accepts if 

$$
\begin{aligned}
y \equiv 
  \begin{cases} u^2x \mod{m} & \text { if } b=0 \\ 
    u^2 \mod{m} & \text { if } b=1 
  \end{cases}
\end{aligned}
$$

​        and rejects otherwise.



You are asked to check:


- (a) **完备性**：如果双方都按照协议行事，那么 Verifier 总是接受。

- (b) **可靠性**：如果 $QR(m, x)=0$（特别是，Prover 不知道任何有效的 $s$ ），那么无论 Prover 做什么，Verifier 都以 $\geq 1 / 2$ 的几率拒绝。

- (b\*) **知识可靠性**：对于任何使得 Verifier 接受的证明算法，如果允许 Verifier 同时查询  $b\in\{0,1\}$  的两个值（对于相同的 $t$  )，则 Verifier 可以从 Prover 中提取秘密 $s$。 （这里的重点是除非证明者“知道”$s$，否则证明者无法说服验证者。）

- (c) **零知识**：无论验证者做什么（验证者的行为可能与协议不同），验证者都可以在不与证明者交互的情况下自行模拟整个交互，这样如果验证者接受，那么记录下来的信息与实际交互几乎不可区分。



------



#### Solution

- 完备性：如果双方都按照协议行事，那么 Verifier 总是接受。
  - 为当 Prover 遵循协议时，他根据 $b$ 的值选择 $u = t$ 或 $u = st$，并确保 $y \equiv u^2x \mod{m}$（如果 $b = 0$）或 $y \equiv u^2 \mod{m}$（如果 $b = 1$）。所以 Verifier 总是会接受。
- 可靠性：如果 $QR(m, x) = 0$（特别是，Prover 不知道任何有效的 $s$），那么无论 Prover 做什么，Verifier 都以 $\geq 1 / 2$ 的几率拒绝。
  - 如果 $QR(m, x) = 0$，那么 $x$ 是模 $m$ 下的二次非剩余。因此，无论 Prover 提供何种 $t$，都无法同时满足 $y \equiv t^2x \mod{m}$ 和 $y \equiv (st)^2 \mod{m}$，因为至少有一个不是二次剩余。由于 $b$ 是随机选择的，所以 Verifier 有至少 1/2 的概率选择一个使得 Prover 无法满足等式的 $b$，从而拒绝 Prover。
- 知识可靠性：对于任何使得 Verifier 接受的证明算法，如果允许 Verifier 同时查询 $b\in{0,1}$ 的两个值（对于相同的 $t$），则 Verifier 可以从 Prover 中提取秘密 $s$。
  - 如果 Verifier 可以同时查询 $b = 0$ 和 $b = 1$，那么它将接收到两个值 $u_0$ 和 $u_1$，对应于 $t$ 和 $st$。从这两个值中，Verifier 可以计算出 $s = u_1 / u_0$，从而获得秘密值 $s$。这表明只有知道 $s$ 的 Prover 才能成功地使 Verifier 接受。
- 零知识：无论验证者做什么（验证者的行为可能与协议不同），验证者都可以在不与证明者交互的情况下自行模拟整个交互，这样如果验证者接受，那么记录下来的信息与实际交互几乎不可区分。
  - Verifier 可以随机选择一个 $b$ 和一个 $u$，并计算 $y = u^2$（如果 $b = 0$）或 $y = u^2x \mod{m}$（如果 $b = 1$）。这将生成与实际交互相同的 transcript，并且不需要知道 $s$。因此，该协议具有零知识性质。





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


答：如果在等式 $\alpha \beta g = y$ 两端同时计算与 $g$ 的映射，得到 $e(\alpha \beta g, g) = e(\alpha g, \beta g) = e(y, g)$ 。由此可以说明等式 $\alpha \beta g = y$ 成立。


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

#### Solution

验证算法可以接受正确的签名。因为在验证 $(pk,m,\sigma)$ 时，等式两端分别为 $e(g_0,\sigma) = e(g_0, \alpha H(m))$ 和 $e(pk,H(m)) = e(\alpha g_0, H(m))= e(g_0,\alpha H(m))$ 。等式两端相等，就说明正确的签名会始终通过验证算法。

选择消息攻击下的存在性不可伪造性：
* 在多项式时间计算出 $\alpha$ 是困难的，因此攻击者很难伪造签名 $\sigma \leftarrow \alpha H(m)$ 。因为这是一个离散对数困难问题;
* 哈希函数具有抗碰撞性，因此无法在多项式时间内找到一个 $m$ ，使得 $H(m)$ 等于一个事先给定的值。所以攻击者想要任意选择消息 $m$ 使得 $H(m)$ 等于一个特定的值，这在多项式时间内也无法做到。
