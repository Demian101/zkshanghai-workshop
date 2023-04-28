# 第2课 课后作业

## Exercise 1 - Num2Bits

-   Parameters: `nBits`
-   Input signal(s): `in`
-   Output signal(s): `b[nBits]`

The output signals should be an array of bits of length `nBits` equivalent to the binary representation of `in`. `b[0]` is the least significant bit. ( `b[0]` 是最低有效位 )

```circom
template Num2Bits(n) {
    signal input in;
    signal output out[n];
    var lc1=0;  // 累加器, make sure 最终的二进制转换结果的整数值与 n 相等

    var e2=1;
    for (var i = 0; i<n; i++) {
        out[i] <-- (in >> i) & 1; // 将 `in` 右移 `i` 位，然后与 1 进行按位与操作
        out[i] * (out[i] -1 ) === 0; // 约束值为 0 or 1
        lc1 += out[i] * e2;  // 累加器累加结果
        e2 = e2+e2;  // e2 * 2: 其值: 1,2,4,8,16 ....
    }

    lc1 === in;  
}
```

-----

```rust
template Num2Bits_strict() {
    signal input in;
    signal output out[254];

    component aliasCheck = AliasCheck();
    component n2b = Num2Bits(254);
    in ==> n2b.in;

    for (var i=0; i<254; i++) {
        n2b.out[i] ==> out[i];
        n2b.out[i] ==> aliasCheck.in[i];
    }
}
```


## Exercise 2 - IsZero

-   Parameters: none
-   Input signal(s): `in`
-   Output signal(s): `out`

Specification: If `in` is zero, `out` should be `1`. If `in` is nonzero, `out` should be `0`. This one is a little tricky!

下面代码为什么这么复杂? 因为使用了信号处理的常见技巧，即: 使用一个` input signal `的倒数来检查其是否为零。

> 由于信号中可能包含噪声和误差，因此我们不能直接比较信号是否等于零。相反，一种更可靠的方法是检查输入信号的倒数是否趋近于无穷大。

然而，在电路中，不能直接使用除法 (我理解是, 在 `<==` 和 `==>` 的两侧不能出现除法 , 但是 `-->` 和 `<--` 可以)，

通过将 `in` 处理为其倒数，可以避免在电路中出现除以 0 的错误。

最后，`in*out === 0` 的意思是输入信号和输出信号必须是相反数或其中一个为零。这个条件是为了确保输出信号是输入信号的相反数或为零。



```circom
template IsZero() {
    signal input in;
    signal output out;

    signal inv;

    inv <-- in!=0 ? 1/in : 0;

    out <== -in*inv +1;
    in*out === 0;
}
```


https://github.com/Antalpha-Labs/zkp-co-learn/discussions/21 : 
1.  只有 `<==` / `===` / `==>`才会被编译到最后的约束系统里
2.  `<--` 和一些其他的计算，是为了方便根据 input 生成 prove用的，即使没有这些计算，prover 手动算出来放到最后 proving 过程里也可以。
3.  之前的错误答案里的计算和赋值，只是实现了“prover不作恶情况下如何根据input计算出正确的值”，并不能防止“恶意的prover用错误值通过verify”， 因为这些没有进入最后的约束里。

inv 是个辅助输入
var 是用来辅助收集 linear combanation

5的inverse 是不是1/5呀？为什么是这么大的数字呢？8755297148735710088898562298102910035419345760166413737479281674630323
> 因为是有限域里的-1次方


## Exercise 3 - IsEqual
-   Input signal(s): `in[2]`
-   Output signal(s): `out`

Specification: If `in[0]` is equal to `in[1]`, `out` should be `1`. Otherwise, `out` should be `0`.

```rust
template IsEqual() {
    signal input in[2];
    signal output out;

    component isz = IsZero();

    in[1] - in[0] ==> isz.in;

    isz.out ==> out;
}
```

## Exercise 4 - LessThan

-   Input signal(s): `in[2]`. Assume that it is known ahead of time that these are at most 2252−12252−1.
-   Output signal(s): `out` 

Specification: If `in[0]` is strictly less than `in[1]`,  `out` should be `1`. Otherwise, `out` should be `0`.
> 如果 `in[0]` 严格小于 `in[1]` ，out 应该是 1。否则，out 应该是 0 。

```rust
template LessThan(n) {
    assert(n <= 252); // 需要 n <= 252
    signal input in[2];
    signal output out;

    component n2b = Num2Bits(n+1);

    n2b.in <== in[0]+ (1<<n) - in[1];

    out <== 1-n2b.out[n];
}
```

## Exercise 5 - Selector

-   参数：`nChoices`
-   输入信号：`in[nChoices]`, `index`
-   输出：`out`

要求：输出 `out` 应该等于`in[index]`。 如果 `index` 越界（不在 `[0, nChoices)` 中），`out` 应该是 `0`。

[https://github.com/darkforest-eth/circuits/blob/master/perlin/QuinSelector.circom]

```rust
include "CalculateTotal.circom"

template QuinSelector(choices) {
    signal input in[choices];
    signal input index;
    signal output out;
    
    // Ensure that index < choices
    component lessThan = LessThan(4);
    lessThan.in[0] <== index;
    lessThan.in[1] <== choices;
    lessThan.out === 1;

    component calcTotal = CalculateTotal(choices);
    component eqs[choices];

    // For each item, check whether its index equals the input index.
    for (var i = 0; i < choices; i ++) {
        eqs[i] = IsEqual();
        eqs[i].in[0] <== i;
        eqs[i].in[1] <== index;

        // eqs[i].out is 1 if the index matches. As such, at most one input to
        // calcTotal is not 0.
        calcTotal.in[i] <== eqs[i].out * in[i];
    }

    // Returns 0 + 0 + 0 + item
    out <== calcTotal.out;
}
```


