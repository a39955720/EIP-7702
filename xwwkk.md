---
timezone: UTC+8
---

> 请在上边的 timezone 添加你的当地时区(UTC)，这会有助于你的打卡状态的自动化更新，如果没有添加，默认为北京时间 UTC+8 时区


# 你的名字

1. 自我介绍
Kevin
2. 你认为你会完成本次残酷学习吗？
会，而且想对安全方面进行更加深度的挖掘。
3. 你的联系方式（推荐 Telegram）
@Kev1nWeb3

## Notes

<!-- Content_START -->

### 2025.05.14

#### Day1 研究EIP-7702的过去：从账户抽象到原生合约钱包的演化
在以太坊中，传统的 EOA（外部拥有账户）功能有限且风险较高，因此，如何对 EOA 账户进行抽象和拓展，一直是许多过去提案试图解决的重要目标。
- EIP-4337
- EIP-3074
- EIP-5003

只有在了解先前这些提案的基础上，我们才能对 EIP-7702 有一个更加完整和深入的认识。
##### EIP-4337
账户抽象（Account Abstraction）
通过高层基础设施实现账户抽象，避免更改共识层协议。主要组件包括：
- 智能合约钱包：链上部署的合约，管理用户账户权限和资源。
- Bundler：EOA钱包，代表用户验证和执行UserOperation交易。
- Entry Point Contract：链上全局合约，Bundler通过它执行UserOperation，充当Bundler与智能合约钱包的中介。
- Paymaster：实现Gas抽象，支持用ERC20代币支付Gas或无Gas交易。
- Wallet Factory：用于创建智能合约钱包的合约。
- Signature Aggregator：优化签名验证，降低交易成本。
整体流程：
1. 用户通过前端构造UserOperation以及签名
2. Bundler收集UserOperation，并验证签名的有效性，将其打包成批量交易（Paymaster介入，可以用ERC20代币支付Gas或无Gas交易； Signature Aggregator用于优化签名验证，降低交易成本）
3. Entry Point Contract收到Bundler的批量交易，调用智能合约钱包执行UserOperation，交易上链。
目前的一些数据（2025/5/14）
ETH：
61,115 Accounts, 277,345 UserOps, 278,291 Transactions, 400.31 ETH gas, 125.8517 ETH revenue(Bundler)
##### EIP-3074
在EVM中引入了两条新指令`AUTH`和`AUTHCALL`，允许外部拥有账户（EOA）将其控制权委托给智能合约，从而实现批量交易、Gas 赞助、交易到期设置和脚本化操作等功能。这增强了 EOA 的灵活性和用户体验，与智能合约钱包的部分功能类似，但无需更改账户类型。
##### EIP-5003
引入了一个新的操作码`AUTHUSURP`
EOA先通过EIP-3074的机制授权某个智能合约，然后`AUTHUSURP`允许将代码直接部署到这个EOA的地址上

### 2025.05.15

#### Day2 EIP-7702
EIP-7702 引入了一种机制，允许 EOA（外部拥有账户）通过授权将执行委托给智能合约，同时保留 EOA 的核心特性。
**工作原理**

1. 用户使用 EOA 私钥签署一条特殊授权消息，指定委托的智能合约
2. 授权包含在交易中，提交至以太坊网络。
3. 网络记录该 EOA 的委托状态，未来对该 EOA 的交易将执行指定智能合约的代码
4. 交易中的 msg.sender 保持为 EOA 地址，而非智能合约地址
**重要保留**

EOA 的私钥保留最终控制权，可通过签署新交易覆盖或撤销任何授权
**主要功能**

1. 交易批处理：允许将多个操作打包成单笔交易，由智能合约执行，提升效率
2. 费用赞助：智能合约可实现 gas 费用由第三方支付，降低用户直接支付的负担
3. 权限管理：智能合约可定义复杂的权限逻辑，增强 EOA 的功能（如限制性执行规则）
EIP-7702是一个平衡创新与实用性的提案，为以太坊用户提供了灵活的账户抽象功能，特别是在交易批处理和费用赞助方面

### 2025.05.18

#### Day5 EIP-7702的安全问题
EIP-7702引入一个新的交易类型，是通过一个委托指针来指定一个智能合约来执行相关逻辑，主要包括一个授权列表包括\[chain_id, address, nonce, y_parity, r, s\]

**可能的漏洞**

1. 权限控制：智能合约中的函数相关的权限控制未合理设置，其他用户可能调用合约来转账授权钱包内的货币
2. 初始化：初始化抢跑，在用户delegate后再进行初始化，初始化操作存在被抢跑风险
3. 存储冲突：delegate调用的合约中的相关存储类型不同，如contractA中为boolean，user 授权 A delegatecall B，contractB中为uint

**具体的例子**

1. 用`tx.origin == msg.sender`阻止合约账户的调用可能存在风险，来防御闪电贷和重入攻击的方式可能存在巨大风险

原因是EOA不能执行代码的基本逻辑被改变了，加大了被攻击的概率
如EOA账户委托给恶意合约，合约在fallbalk函数中对目标合约进行重入；EOA账户委托给闪电贷合约，贷取一定的货币进行套利，原有的防御无法判断是否为合约调用

2. 合约地址判断的失效（isContract(address)）

通过`extcodesize/code.length`来判断是否是合约地址可能会失效，原因是有委托的EOA账户中也存有字节码，无法正确判断

3. EOA账户接受代币异常

原先对EOA账户的转账是非常简单的，可是EIP-7702后，对有委托的EOA账户进行转账可以也存在拒绝收款的操作（实际上调用了委托合约的fallback函数）

**安全建议**

设计新合约时，请假设“无代码”的EOA账户最终可能不复存在`tx.origin`。

### 2025.05.19

计划利用EIP-7702实现自由组合多笔交易的合约

#### 规划阶段

目标：深度理解 EIP-7702 的应用场景。

功能需求：
支持用户通过单个 EIP-7702 交易调用多个智能合约操作，如转账、代币交换、NFT 铸造等。

允许动态指定目标合约、函数调用和参数，实现交易的自由组合。

优化 Gas 消耗，通过批量执行减少多次交易的开销。

确保安全性，处理异常情况（如部分交易失败）。

提供用户友好接口，方便提交批量交易请求。



<!-- Content_END -->


