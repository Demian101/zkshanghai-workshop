# 第4课 课后作业

## 第1题 Circom

### Semaphore Setup:

```bash

npx @semaphore-protocol/cli@latest create my-app --template monorepo-ethers

npm i -g @semaphore-protocol/cli@latest
semaphore create my-app --template monorepo-ethers

cd my-app
yarn
```

<img src="http://imagesoda.oss-cn-beijing.aliyuncs.com/Sodaoo/2023-07-06-145528.png" width="70%" />



Semaphore源代码导读 - Star.Li  https://zhuanlan.zhihu.com/p/96662660
https://medium.com/swf-lab/zk-app-client-implementation-with-semaphore-431e49dc32b8
Semaphore FAQs: https://hackmd.io/@cedoor/rJ6x5Kgfi


### usage 

https://semaphore.appliedzkp.org/
demo: https://semaphore.appliedzkp.org/
paper: https://docs.zkproof.org/pages/standards/accepted-workshop3/proposal-semaphore.pdf

It allows a user to broadcast their support of an arbitrary string, without revealing who they are to anyone, besides being approved to do so. Semaphore is meant to be used a base layer for signaling-based applications - mixers, anonymous DAOs, anonymous journalism, etc.

Semaphore 是一个去中心化的匿名登入与身份证明系统。想法是在合约中有一个 Merkle tree 的 membership proof，可以不公开密码也能证明自己是某个群体中（某颗 merkle tree）的成员（merkle tree 的其中一个 leaf）。

Semaphore 可以让用户在智能合约上注册一个 `Idendity`（身份），并且广播（Broadcast）他们的讯号（Signal），特点在于：
1. 可以匿名的证明他们的身份存在某一个群体 `Group` 之中
2. 公开地储存（Broadcast）任意字符串（Signal）在合约之中，这类似于 nonce 的 External Nullifier 存在，如果在 External Nullifier 的情况下，有对同一个字串做 double-signalling 那就是非法的

实际上有 nullfier 和 path index 就已经足够证明了，还需要 External Nullifier 是因为其作为 Nonce 作用，能让一个 Identity 可以多次的在同一轮（Merkle Tree）中对同一个 Signal 进行广播。

在 External Nullifier 不同的情况下，就会生成不同的 nullifier hash。

----

当使用者注册了他们的 identity，Semaphore 会产生一组 EdDSA 公私钥对 $pubKey/privKey$，并且将 $publickey$ 和**两个随机秘密值**哈希之后送去给合约，储存在 Merkle Tree 中（就是加入一个 Group）。这个 Hash 就是 identity Commitment，而两个随机秘密值则为 `identityNullifier` 和 `identityTrapdoor`。

<img src="http://imagesoda.oss-cn-beijing.aliyuncs.com/Sodaoo/2023-07-07-013240.png" width="70%" />


接下来使用者就能用他们的 privKey 来计算公钥 pubKey，再用 pubKey 产生该 merkle tree 的 merkle proof。

在此 circuit 中，只要公开该 merkle tree 的 root，其他使用者即可知道此使用者是这个 group 的成员

ZKP 的部分则是在验证公私钥对以及验证公钥是某个 merkle tree 的 leaf。用户能够在 Semaphore 提供的前端（client 端、链下）产生他们在某个 group 中的 proof。将 proof 送到合约中时就可以验证这个 proof 是否 valid。

#### Identities

Users interact with the protocol using a Semaphore [identity](https://semaphore.appliedzkp.org/docs/guides/identities) (similar to Ethereum accounts).  
  (用户通过信号量身份（类似于以太坊账户）与协议进行交互) , It contains three values:

1. `Trapdoor`:  private, known only by user
2. `Nullifier`:  private, known only by user
3. `Commitment`:  public

- Trapdoor: 346964564135116690196357319592556437504057286187971008699125737118531427130
- Nullifier: 149817374314008942672542895477380763033174566593828032028639004282665621677
- Commitment: 370288471661996252279055686108776701601342605514298002717323799512783891772

#### Groups:

Semaphore groups are binary incremental Merkle trees in which each leaf contains an identity commitment for a user. Groups can be abstracted to represent events, polls, or organizations.
  ( 信号量组是二进制递增的默克尔树，其中每个叶子包含用户的身份承诺。可以将这些组抽象为表示事件、投票或组织。)



#### Proofs

Semaphore members can anonymously [prove](https://semaphore.appliedzkp.org/docs/guides/proofs) that they are part of a group and that they are generating their own **signals**. 
Signals could be anonymous votes, leaks, reviews, or feedback.

信号量成员可以匿名证明他们是一个团体的一部分，并且他们正在产生自己的信号。这些信号可以是匿名投票、泄漏、评论或反馈。

<img src="http://imagesoda.oss-cn-beijing.aliyuncs.com/Sodaoo/2023-07-06-145528.png" width="70%" />



### Structure

[semaphore.circom](https://github.com/semaphore-protocol/semaphore/blob/main/packages/circuits/semaphore.circom)
[Semaphore.sol](https://github.com/semaphore-protocol/semaphore/blob/main/packages/contracts/contracts/Semaphore.sol)
[proof](https://github.com/semaphore-protocol/semaphore/tree/main/packages/proof)
- [generateProof.ts](https://github.com/semaphore-protocol/semaphore/blob/main/packages/proof/src/generateProof.ts)
- [verifyProof.ts](https://github.com/semaphore-protocol/semaphore/blob/main/packages/proof/src/verifyProof.ts)
- [calculateNullifierHash.ts](https://github.com/semaphore-protocol/semaphore/blob/main/packages/proof/src/calculateNullifierHash.ts)

#### 1. Key & Identity

使用 Semaphore 的每个账户需要创建私钥 `secret` 和公钥 `commitment`

 - `trapdoor`:   陷门, 是个随机数, 在本地浏览器生成和存储, 不存到合约
 - `nullifier`: 无效符, 是个随机数, 在本地浏览器生成和存储, 不存到合约 
 - `secret`:  可看做私钥, 是个随机数, 在本地浏览器生成和存储, 不存到合约
 - `commitment`:  承诺, 身份标识, 类似以太坊公钥, 存储到合约里.

对  `trapdoor`   和  `nullifier`  做 Poseidon Hash 得到 `secret`
对  `secret` 再做 Poseidon Hash 得到  `commitment` 

```ts
export function genRandomNumber(numberOfBytes = 31): bigint {
    return BigNumber.from(randomBytes(numberOfBytes)).toBigInt()
}

// semaphore/packages/identity/src/identity.ts
export default class Identity {
    private _trapdoor: bigint  // 陷门, 是个随机数, 在本地浏览器生成, 不存到合约
    private _nullifier: bigint //无效符, 是个随机数, 在本地浏览器生成, 不存到合约
    private _secret: bigint    // 私钥, 是个随机数, 在本地浏览器生成, 不存到合约
    private _commitment: bigint // 承诺, 身份标识, 类似以太坊公钥, 存储到合约里.

    constructor(identityOrMessage?: string) {
        if (identityOrMessage === undefined) {
            this._trapdoor = genRandomNumber()
            this._nullifier = genRandomNumber()
            this._secret = poseidon2([this._nullifier, this._trapdoor]) //hash
            this._commitment = poseidon1([this._secret]) //hash

            return
        }

        checkParameter(identityOrMessage, "identityOrMessage", "string")

        if (!isJsonArray(identityOrMessage)) {
            const h = hash.sha512(identityOrMessage).padStart(128, "0")
            // alt_bn128 is 253.6 bits, so we can safely use 253 bits.
            this._trapdoor = BigInt(`0x${h.slice(64)}`) >> BigInt(3)
            this._nullifier = BigInt(`0x${h.slice(0, 64)}`) >> BigInt(3)
            this._secret = poseidon2([this._nullifier, this._trapdoor])
            this._commitment = poseidon1([this._secret])

            return
        }

        const [trapdoor, nullifier] = JSON.parse(identityOrMessage)

        this._trapdoor = BigNumber.from(trapdoor).toBigInt()
        this._nullifier = BigNumber.from(nullifier).toBigInt()
        this._secret = poseidon2([this._nullifier, this._trapdoor])
        this._commitment = poseidon1([this._secret])
    }
```

`hash.sha512(identityOrMessage).padStart(128, "0")` : 
 - 生成了一个 512 位的哈希值（h），它包含了128个十六进制字符。然后通过 `slice` 方法将这个哈希值切分成两部分，每部分为64个字符，对应的位数为64 * 4=256 位。
`alt_bn128 is 253.6 bits, so we can safely use 253 bits.`
 - `alt_bn128` 是 Ethereum 使用的一种配对友好的椭圆曲线，在该曲线上进行的运算，所涉及到的数字的位数不能超过这个上限。因此，为了确保得到的 `_trapdoor` 和 `_nullifier` 位数不会超过 253，对生成的 256 位数进行右移操作，移位 3 位，即除以 2 的 3 次方，相当于取前 253 位。

#### 2. Semaphore.sol

[Semaphore.sol](https://github.com/semaphore-protocol/semaphore/blob/main/packages/contracts/contracts/Semaphore.sol)  中

`IncrementalBinaryTree.sol` : 
  -  `@zk-kit` 是 `PSE` 开发的一套 zk 工具
  - `IncrementalBinaryTree` Solidity script is a library for managing an Incremental Binary Merkle Tree using the Poseidon hash function. **This type of tree allows to compute the root hash every time a leaf is inserted**, maintaining the integrity of the tree. It also supports leaf updates and removals.
  - 所以 `merkleTrees` 每次向某个 Group 插入一个 `identityCommitment` 都会更新其 root


```rust
// semaphore/packages/contracts/contracts/base/SemaphoreGroups.sol
// 引入
import "@zk-kit/incremental-merkle-tree.sol/IncrementalBinaryTree.sol";

using IncrementalBinaryTree for IncrementalTreeData; // 关联类型, 可直接调用其函数

mapping(uint256 => IncrementalTreeData) internal merkleTrees;

function _addMember(uint256 groupId, uint256 identityCommitment) internal virtual {
	if (getMerkleTreeDepth(groupId) == 0) {
		revert Semaphore__GroupDoesNotExist();
	}

	merkleTrees[groupId].insert(identityCommitment);

	uint256 merkleTreeRoot = getMerkleTreeRoot(groupId);
	uint256 index = getNumberOfMerkleTreeLeaves(groupId) - 1;

	emit MemberAdded(groupId, index, identityCommitment, merkleTreeRoot);
}

// @dev See {ISemaphore-addMembers}.
// 批量向 Group 中添加用户
function addMembers(
	uint256 groupId,
	uint256[] calldata identityCommitments
) external override onlyGroupAdmin(groupId) {
	for (uint256 i = 0; i < identityCommitments.length; ) {
		_addMember(groupId, identityCommitments[i]);

		unchecked {
			++i;
		}
	}

	uint256 merkleTreeRoot = getMerkleTreeRoot(groupId);

	groups[groupId].merkleRootCreationDates[merkleTreeRoot] = block.timestamp;
}
```

`Identity` 所对应的 `commitment` 会添加到一个 `merkle` 树上，merkle root 更新, 同时新的 `merkle root` 会记录在root_history的mapping中。


#### 3. Nullifier Hash

Nullifier Hash是用来证明某个Identity对应commitment存在一个merkle树上，并生成的标示。Nullfier Hash的计算过程可以查看电路的逻辑（semaphorejs/snark/semaphore-base.circom）

`packages/circuits/semaphore.circom`

```rust
template CalculateNullifierHash() {
    signal input externalNullifier;  // 不同应用 app 有不同的 externalNullifier, 能让用户的 identityNullifier 进行不同 app 里的复用
    signal input identityNullifier;  // 用户自己的随机数
    signal output out;

    component poseidon = Poseidon(2);
    poseidon.inputs[0] <== externalNullifier;
    poseidon.inputs[1] <== identityNullifier;
    out <== poseidon.out;  // 对 externalNullifier 和 identityNullifier 做 hash
}

template Semaphore(nLevels) {
    signal output nullifierHash;
	// ...
	component calculateNullifierHash = CalculateNullifierHash();
    calculateNullifierHash.externalNullifier <== externalNullifier;
    calculateNullifierHash.identityNullifier <== identityNullifier;
    // ...    
    nullifierHash <== calculateNullifierHash.out;
}
component main {public [signalHash, externalNullifier]} = Semaphore(20);
```

相对比  `TornadoCash` 中的一个 `Nullifier`。  额外加入了 `externalNullifier` 的原因是，`同一个Identity`，在输入  `externalNullifier`  不同的情况下，能生成不同的 `nullifierHash`。

也就是说，一个账户可以多次“消费”。这样设计的原因是为了 `Signal` 的业务需求。


#### 4. signal

创建了 `Identity` 后，就可以发信号（Signal）了。一个账户发送信号，必须首先提供 Identity 存储在 merkle 树上的证明（能计算出 commitment ）。智能合约中的 broadcastSignal 是发送信号的接口：

```rust
function broadcastSignal(
   bytes memory signal,
   uint[2] memory a,
   uint[2][2] memory b,
   uint[2] memory c,
   uint[4] memory input // (root, nullifiers_hash, signal_hash, external_nullifier)
) public
   style="box-sizing: border-box; padding-right: 0.1px;">   isValidSignalAndProof(signal, a, b, c, input)
{
   uint nullifiers_hash = input[1];
   signals[current_signal_index++] = signal;
   nullifier_hash_history[nullifiers_hash] = true;
   emit SignalBroadcast(signal, nullifiers_hash, input[3]);
}
```

signal就是需要发送的信号，a/b/c是零知识证明的proof信息，input是零知识证明对应电路的输入，包括merkle树根，nullifier hash，signal hash以及external nullifier。

只有在proof信息验证过后，对应signal才会记录。每次发送signal时对应的nullifier hash会被记录下来。也就是说，在external nullifier不变的情况下，所有Identity只能发送一次Signal。


### **总结：**

Semaphore项目由js开发，结合零知识证明(zk-SNARK)，在以太坊的智能合约的基础上实现Identity。每个Identity可以发送信号。在external nullifier不变的情况下，每个Identity只能发送一次Signal。
