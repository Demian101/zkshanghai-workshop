# 第5课 课后作业

## 第1题 

The Setup phase of the KZG polynomial commitment scheme involves computing commitments to powers of a secret evaluation point τ . This is called the “trusted setup" and is often generated in a multi-party computation known as the “Powers of Tau" ceremony. One day, you find the value of τ on a slip of paper. How can you use it to make a fake KZG opening proof?

| 如果有一天，在纸条上发现了 τ 的值，如何使用这个值来做一个假的 KZG 证明？

$\tau$ 作为 Setup 阶段的 Toxic waste （Secret），是绝对不能被人知道的，这也是 KZG 承诺有效的保证
如果泄露，我就可以自己构造一个 fake 的证明：like this：


An important vulnerability to be aware of is that if we know $α$, we can easily break **binding** by finding two polynomials that evaluate to the same point (找到两个计算结果相同的多项式 break `binding` ) :

$$
\begin{aligned} 
if  \ \ we  \ \  know: \ \ {\alpha} = 3 \\ 
p_{1}\left(x\right) = x^{3} + 10 \, x^{2} + 8 \, x + 6 \\ p_{2}\left(x\right) = 7 \, x^{2} + 19 \, x + 27 \\ g^{p_{1}\left({\alpha}\right)} = g^{p_{2}\left({\alpha}\right)} \\ g^{{\alpha}^{3} + 10 \, {\alpha}^{2} + 8 \, {\alpha} + 6} = g^{7 \, {\alpha}^{2} + 19 \, {\alpha} + 27} \\ g^{147} = g^{147} \end{aligned}
$$

Luckily we can rely on the **t-polyDH** assumption (an extension of **q-SDH**) to help us establish **hiding** and **binding** and prevent this vulnerability.


## 第 2 题 

Construct a vector commitment scheme from the KZG polynomial commitment scheme. (Hint: For a vector m = (m1, . . . , mq), is there an “interpolation polynomial" I(X) such that I(i) = m[i]?)

| 从 KZG 多项式承诺方案构造一个向量承诺方案。

To construct a vector commitment scheme from the KZG polynomial commitment scheme, one could associate each element in the vector with a distinct point in the field over which the polynomial is defined, and construct a polynomial that passes through these points.

Let's say you have a vector m = (m1, m2, ..., mq). You can define an interpolation polynomial I(X) such that I(i) = m[i] for each i in 1 to q. That is, the polynomial passes through the point (i, m[i]) for each i. This can be done using, for example, Lagrange interpolation.

You can then use the KZG polynomial commitment scheme to commit to this polynomial. The commitment can later be used to prove that I(i) = m[i] for any i, which proves that the i-th element of the vector m is m[i]. This proof only reveals the value m[i] and the index i, and no other information about the vector m. The proof can be verified using the commitment and the properties of the KZG scheme.

This essentially transforms the KZG polynomial commitment scheme into a vector commitment scheme, where you can commit to a vector and later prove facts about individual elements of the vector. The nice property about this approach is that the size of the commitment and the size of the proof do not depend on the size of the vector m, they are constant.

## 第 3 题 
The KZG polynomial commitment scheme makes an opening proof π for the relation p(x) = y. Can you extend the scheme to produce a multiproof π, that convinces us of p(xi) = yi for a list of points and evaluations (xi , yi)? (Hint: assume that you have an interpolation polynomial I(X) such that I(xi) = yi .

拓展这个KZG多项式承诺，从而产生一个多重证明，来使得 p(xi) = yi 有一系列的点和评估(xi,yi)


$t$  个多项式，打开一个随机点, 进行批量验证

初始化 : 双线性群为  $\mathcal{G} = (e, \ \ \mathbb{G}_1, \ \ \mathbb{G}_2 , \ \ \mathbb{G}_T)$ , 随机数  $s \in_{R} {\mathbb{Z}_{p}}^{*}$  , $d+2$  元组   

$$\langle {\color{orange}G_1, \ \  s \cdot G_1 , \ \  s^2 \cdot G , \ \  , \dots, s^{d-1} \cdot G_1}  \ \ ; {\color{blue} \ \ G_2, \ \ sG_2}\rangle \ \  \in \ \ ({\mathbb{G}_{1}}^{d}, \ \ {\mathbb{G}_{2}}^{d})$$
让输出为 : 

$$
PK =(\mathcal{G}, \ \  {\color{orange}G_1, \ \  s \cdot G_1 , \ \  s^2 \cdot G , \ \  , \dots, s^{d-1} \cdot G_1}  \ \ ; {\color{blue} \ \ G_2, \ \ sG_2})
$$

如下是 $\color{red}t \ \ 个$  多项式  

$$f(x)=\sum_{j=0}^{deg(f(i))}  f_{i, \ j} \ \cdot x^{j} \ \ \ \ ; \ \ \ i=1 \dots t $$

>  类比上个 `chapter`,  $f_{i, \ j}$  表示 **第  $i$  个多项式的第 $j$ 个系数**

承诺:  对 $\color{red}t \ \ 个$  多项式, 分别计算 $t$ 个多项式承诺 :

$$
C_i = f_{i}(s) \cdot G_1 = \sum_{j=0}^{deg(f_i)} \ f_{i,\ j} \ \cdot s^{j} \cdot G_1 \ \ ; \ \ \ {\color{purple}i= 1 \dots t}
$$

打开随机点 :  基于上述多项式与承诺, 基于 $transcript$  计算随机数  $\gamma$  .  计算在 $z$  点的 **商多项式** : 

$$
q_{i}(x) = \sum_{i=1}^{t} \ \gamma^{i-1} \ \cdot \frac{f_{i}(x) - f_{i}(z) }{ x - z}
$$

>  $\gamma$  的作用:  使用 $\gamma$  即 `Hash` 出来的随机数对这些多项式进行线性组合

计算**商多项式承诺** :

$$
C_{q_{i}(x)} = f_{i}(s) \cdot G_1
$$

输出 $(z, \ \ f_{i}(z), \ \ C_{q_{i}(x)})$  

验证随机点 : 同样基于上述多项式与 Commitment, 基于 $transcript$  计算随机数  $\gamma$  

分别计算  $t$  个多项式 `Commitments`  $C_i$   和  在函数点值处的 $f(z)$  `Commitments`  的累加值 : 

$$
\begin{aligned}
F = \sum_{i=1}^{t} \ \gamma^{i-1} \ \cdot C_{i} &= \sum_{i=1}^{t}  \gamma^{i-1}  \ \cdot f_i(s) \ \cdot G_1 \\
在 \ \ z \ \  点打开: \quad V &= \sum_{i=1}^{t} {\gamma}^{i-1} \ \cdot  f_i(z) \cdot G_1
\end{aligned}
$$

如果以下等式成立, 则接受, 否则拒绝 : 

$$
e(F-V, \ \ G_2) \cdot e(- C_{q_{i}(x)} , \ \ s \cdot G_2 -z \cdot G_2)= 1
$$

公式推导如下 :

$$
\begin{aligned}
& \quad e(F-V, \ \ G_2) \cdot e(- C_{q_{i}(x)} , \ \ s \cdot G_2 -z \cdot G_2) \\
&=e\left( {\color{red}\sum_{i=1}^{t} \gamma^{i-1} \ \cdot \left( f_i(s) - f_i(z)\right)} \cdot G_1 , \ \ G_2 \right) \cdot e ( {\color{blue} -q_{i}(s) } \cdot G_1  , \ \ {\color{blue}(s-z)} \cdot G_2  )  \\
&= e(G_1, G_2)^{\color{red}\sum_{i=1}^{t} \gamma^{i-1} \ \cdot \left(  f_i(s) - f_i(z)\right)} \cdot e(G_1, G_2)^{ {\color{blue} -q_{i}(s) \cdot (s-z)}} \\
&= e(G_1, G_2)^{\color{red}\sum_{i=1}^{t} \gamma^{i-1} \ \cdot \left(  f_i(s) - f_i(z)\right)}  \cdot e(G_1, G_2)^{ {\color{blue}-\sum_{i=1}^{t} \ \gamma^{i-1} \ \cdot \frac{f_{i}(s) - f_{i}(z) }{ s - z} \cdot (s-z)} } \\
&=e(G_1, G_2)^{ (1+-1) \cdot \sum_{i=1}^{t} \ \gamma^{i-1} \ \cdot \frac{f_{i}(s) - f_{i}(z) }{ s - z}} \\
&=e(G_1, G_2)^{0} \\
&={\color{red}1}
\end{aligned}
$$

上式蓝色部分解析 :

$${\color{blue}q_{i}(s)} =\sum_{i=1}^{t} \ \gamma^{i-1} \ \cdot \frac{f_{i}(x) - f_{i}(z) }{ x - z} \ \  evaluate  \ \ at \ \ s: \quad =\sum_{i=1}^{t} \ \gamma^{i-1} \ \cdot \frac{f_{i}(s) - f_{i}(z) }{ s - z}$$



