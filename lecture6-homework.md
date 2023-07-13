# 第6课 课后作业# 

## 第1题 修改课堂上的 Fibonacci 程序至宽度为 3 的 AIR  

| step |  a   |  b   |
| :--: | :--: | :--: |
| i=1  |  1   |  1   |
| i=2  |  2   |  3   |
| i=3  |  5   |  8   |
| i=4  |  13  |  21  |


转化程序：


$$
f_1(X_1, X_2,  {X_1}^{next}, {X_2}^{next})={\color{red}A^{next}}-(B+A) \\
f_2(X_1, X_2,  {X_1}^{next}, {X_2}^{next})={\color{red}B^{next}}-(B+A^{next}) \\
$$


举例：如上图，第 $i=2$ 行的转换：


$$
f_1(X_1, X_2,  {X_1}^{next}, {X_2}^{next})={\color{red}5}-(3+2) = 0\\
f_2(X_1, X_2,  {X_1}^{next}, {X_2}^{next})={\color{red}8}-(3+5) = 0 \\
$$

> 其中 $A,B$ 是对 $a,b$ 列元素进行拉格朗日插值得到的多项式函数。  



现在，要将该 Fibonacci 程序修改至宽度为 3 的 AIR：

| step |  a   |  b   |  c   |
| :--: | :--: | :--: | :--: |
| i=1  |  1   |  1   |  2   |
| i=2  |  3   |  5   |  8   |
| i=3  |  13  |  21  |  34  |
| i=4  |  55  |  89  | 144  |



转化程序：
$$
\begin{aligned}
&f_1(X_1, X_2, X_3, {X_1}^{next}, {X_2}^{next}, {X_3}^{next})=A^{next}-(B+C) \\
&f_2(X_1, X_2, X_3, {X_1}^{next}, {X_2}^{next}, {X_3}^{next})=B^{next}-(C+A^{next}) \\
&f_3(X_1, X_2, X_3, {X_1}^{next}, {X_2}^{next}, {X_3}^{next})=C^{next}-(A^{next}+B^{next}) 
\end{aligned}
$$
第 $i=2$ 行举例：


$$
\begin{aligned}
& A^{next}-(B+C) =13-(5+8) =0 \\
& B^{next}-(C+A^{next}) = 21-(8+13) =0 \\
& C^{next}-(A^{next}+B^{next})  =34 - (13+21) =0
\end{aligned}
$$








## 第2题 你能写一个仅在行 $i=1$ 上应用RAP的多重集合相等性检查的约束吗？

Randomized AIR with Preprocessing：

| step  |   a   |   b   |                              c                               |
| :---: | :---: | :---: | :----------------------------------------------------------: |
| $i=1$ | $a_1$ | $b_1$ |                              1                               |
| $i=2$ | $a_2$ | $b_2$ |               $\frac{a_1+\gamma}{a_2+\gamma}$                |
| $i=3$ | $a_3$ | $b_3$ | $\frac{(a_1+\gamma)(a_2+\gamma)}{(a_2+\gamma)(a_3+\gamma)}$  |
| $i=4$ |   0   |   0   | $\frac{(a_1+\gamma)(a_2+\gamma)(a_3+\gamma)}{(a_2+\gamma)(a_3+\gamma)({\color{red}a_1}+\gamma)}$ |



目标：多重集合相等性检查

1. 列 $a$  和 $b$ 包含了你想要检查相等性的两个多重集合的元素，而列 $c$  是用来构建你的零知识证明的。
2. 随机数 $\gamma$  用于随机化你的证明 ，增加其安全性
3. 使用了一个特定的函数（在这个例子中是乘积函数）来将列  $a$  和 $b$ 转化为列 $c$  . 这个函数是你的约束多项式，它定义了  $a$  和 $b$ 之间的关系。
4. 在每一行 $i$中，都计算了 $\prod_{1 \leq j \leq i}\left(a_j+\gamma\right) /\left(b_j+\gamma\right)$  并将结果存储在 $c_i$ —— 生成新列 $z$ 。
5. 最后，检查 $Z^{next} \cdot (B+ \gamma ) -Z \cdot (A+\gamma) = 0$  ，确保 $a$ 和 $b$  是相等的。如果这个约束不满足，那么你就知道  $a$ 和 $b$   不相等。如果满足，那么你就得到了一个证明  $a$ 和 $b$   相等的零知识证明。

这个过程是一个标准的 RAP 过程，它使用了随机化（ $\gamma$ ）和预处理（通过计算列 $c$）来生成一个零知识证明。这个证明可以被任何人验证，但是它不揭露任何关于  $a$  和 $b$  的具体信息，只揭露了它们是否相等。



约束多项式为：


$$
\prod_{i \in[n]}\left(a_i+\gamma\right)=\prod_{i \in[n]}\left(b_i+\gamma\right) \Longrightarrow \prod_{i \in[n]}\left(a_i+\gamma\right) /\left(b_i+\gamma\right)=1
$$


构建 $z$ 列：


$$
z_i=\prod_{1 \leq j \leq i}\left(a_j+\gamma\right) /\left(b_j+\gamma\right)
$$
检查约束：


$$
Z^{next} \cdot (B+ \gamma ) -Z \cdot (A+\gamma) = 0
$$


假如第 $i=2$ 行：


$$
\begin{aligned}
&\frac{\left(a_1+\gamma\right)\left(a_2+\gamma\right)}{\left(a_2+\gamma\right)\left(a_3+\gamma\right)} \cdot\left(a_3+\gamma\right)-\frac{\left(a_1+\gamma\right)}{\left(a_2+\gamma\right)} \cdot\left(a_2+\gamma\right) \\
&=(a_1+\gamma) - (a_1+\gamma)  \quad \quad  // 约分  \\
&=0
\end{aligned}
$$




仅在行 $i=1$ 上应用RAP的多重集合相等性检查的约束：

生成两列元素 $R$ 和 $S$ ，其中 $S$ 只有 $S_1=1$ ，其他 $S$ 等于0， $R$ 只有 $R_1=0$ ，其他 $R$ 均等于1。

令约束多项式函数为： 


$$
\prod_{i \in {|n|}} (S_i \cdot (a_i+r) / (b_i+r) + R_i)=1
$$


- 当 $i=1$ 该约束退化为标准的多重集合相等性检查的约束性函数；
- 当 $i>1$ 时，该约束两边恒相等，综上该约束只在 $i=1$ 时生效。



在这个函数中，$a_i$ 和 $b_i$ 是我们要检查相等性的两个集合的元素，$r$  是一个随机数。

- 当 $i=1$  的时候，由于 $S_1=1$  和 $R_1=0$ ，这个函数就变成了 $\frac{a_1+r}{b_1+r} = 1$ ，这是一个标准的集合相等性检查的约束。
- 当 $i>1$  的时候，由于  $S_i=0$  和 $R_i=1$  ，这个函数就变成了 $1=1$，这是一个恒等式，所以不会影响结果。



这样，我们就得到了一个只在 $i=1$ 上生效的约束。这个约束能够在不揭示集合内容的情况下，检查 $a_1$ 和 $b_1$ 是否相等。这是一种在零知识证明中常用的技巧，它允许我们在保证隐私的同时，验证一些重要的性质。
