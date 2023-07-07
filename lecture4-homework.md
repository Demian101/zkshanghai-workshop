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


### usage 

https://semaphore.appliedzkp.org/
demo: https://semaphore.appliedzkp.org/
paper: https://docs.zkproof.org/pages/standards/accepted-workshop3/proposal-semaphore.pdf

It allows a user to broadcast their support of an arbitrary string, without revealing who they are to anyone, besides being approved to do so. Semaphore is meant to be used a base layer for signaling-based applications - mixers, anonymous DAOs, anonymous journalism, etc.


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
