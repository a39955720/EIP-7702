---
timezone: UTC+8
---

> 请在上边的 timezone 添加你的当地时区(UTC)，这会有助于你的打卡状态的自动化更新，如果没有添加，默认为北京时间 UTC+8 时区


# 你的名字

嗨大家我是Univ，目前正在学习Web3领域知识，是我的荣幸可以跟大家一起学习，一起分享学习知识
你认为你会完成本次残酷学习吗？ 我会完成这次的共学
你的联系方式（推荐 Telegram）t.me/Univ

## Notes

<!-- Content_START -->

### 2025.05.14

### 1. 為什麼 EIP‑7702？

* **Pectra 硬分叉時間點**：官方 2025‑05‑07 上線。也就是說，任何拿 MetaMask、Rainbow 之類「傳統 EOA」的朋友，都能瞬間獲得「智慧合約錢包級」的超能力。對開發者和使用者都是大新聞。  
* **我自己的痛點**：我在做一個包含 DEX + NFT 市場的小專案。現在最讓新人崩潰的流程是：  
  1. 第一次「Approve」鎖代幣（要付一次 gas）。  
  2. 第二次才「Transfer / Swap」（再付一次 gas）。  
  如果他們還沒存 ETH，就直接卡關。**7702** 把這些痛全部揉成一顆藥丸吞下去，一條龍搞定。  
* **錢包 UX 全面升級**：想像一下，你跟朋友吃飯，有個 DApp 說「我幫你付 gas，你只要按一次確認」。過去要靠 4337 或自家伺服器，門檻高；7702 出來，人人都能辦到。

---

### 2. 我眼中的三大亮點

| 亮點 | 白話翻譯 | 真的有什麼爽點？ |
|------|---------|-------------------|
| **批次交易 (Batching)** | 把「一次要做很多件事」打包成「一筆就搞定」。 | 少簽一次 = 少按一次 = 少一次搞錯的機會；gas 也省一大截。 |
| **交易贊助 (Sponsorship)** | 別人幫你出 gas，或用 USDC 之類代幣付費。 | 沒 ETH 也能玩連署、鑄 NFT、打 GameFi，門檻瞬間降到 0。 |
| **降權／會話金鑰 (Session Keys)** | 給 DApp 一把「只能花 500 塊、限 30 分鐘」的小鑰匙。 | 釣魚也只能咬到小額，提款機被裝護欄，安全感 up。 |

👉 重點：**7702 = 「臨時」把 EOA 的程式碼指向代理合約**。它不像 4337 得搞一大堆 Bundler、EntryPoint；只有在那一筆交易、那幾分鐘內生效，用完就收工，簡單粗暴。

---

### 3. 跟 ERC‑4337 是兄弟還是對手？

* 我覺得兩者是 **「大哥 + 小弟」** 的關係，不是你死我活。  
  * **ERC‑4337**：超完整、模組化、可擴充，像一台重裝機甲——功能強，部署重。  
  * **EIP‑7702**：輕量、即插即用，像袖珍瑞士刀——不一定能打大 Boss，但日常超好用。  
* 更棒的是，**Smart EOA**（開了 7702 超能力的地址）可以直接塞進 4337 的 `UserOperation` 當 sender。所以生態系不會破碎，大家繼續用熟悉的工具鏈。

---

### 4. 我最擔心／還沒解的問題

1. **私鑰還是單點失敗**  
   * 7702 幫你搞定「我忘記私鑰」→ 社交恢復；但「私鑰被盜」還是沒救。  
   * 後續要靠 EIP‑7851 那種「停用 / 重啟私鑰」功能才真正安全。  
2. **`tx.origin` 不再專屬最外層**  
   * 老智能合約用 `require(msg.sender == tx.origin)` 來防閃電貸、重入，現在可能被繞過。  
   * 必須快點跑一次審計，把舊程式碼掃過。  
3. **授權列表 UX 超關鍵**  
   * `authorization_list` 其實就是「把帳號大門鑰匙交給某段程式碼」。  
   * 如果錢包沒有做白名單、沒有顯示易懂警告，使用者簽一下就可能 GG。  
4. **節點 & 交易池 新麻煩**  
   * 一個 Smart EOA 可以在一筆交易裡狂呼多次 call，導致別人掛在 mempool 的交易瞬間失效。  
   * 節點端建議：對「已委派且 code 非空」的 EOA，一次只收一筆待處理。

---

### 5. 我準備怎麼用 7702？

* **前端**：  
  * 先偵測錢包是否支援 7702（簡單判 `eth_supportedTxTypes`）。  
  * 若支援，就把「Approve + Swap」合併成「一鍵 Swap」的 UX。  
* **後端（小型贊助服務）**：  
  * 幫用戶墊 gas，但收小額 USDC；跑一個簡易聲望 + 押金機制，避免白嫖。  
* **安全監控**：  
  * 把所有授權的目標地址拉進監控，看 code hash 是否在白名單；一旦有未知合約即時警告。

---

### 6. 接下來要觀察哪些指標？

1. 分叉後 **三個月**：  
   * 7702 交易量 vs. 4337 UserOp 的比例，如果後者還是大宗，就代表輕量需求沒那麼強烈。  
2. **錢包支援速度**：  
   * MetaMask、Rainbow、Safe、Keystone（硬體錢包）誰先搶頭香？  
3. **Hackathon 生態**：  
   * 會不會誕生「Session Key SDK」、「Gas Sponsorship API」這種新中介服務。  
4. **安全事件**：  
   * 有沒有人因亂簽 `authorization_list` 被洗光？那會是最血淋淋的教材。

---

### 7. 技術細節 & 成本解析（Rationale 大補帖）

#### 7‑1 授權成本怎麼算？

| 動作 | Gas 估算 | 一句話解釋 |
|------|---------|-----------|
| 傳 101 bytes calldata | 1616 | 每非零 byte 乘 16 |
| `ecrecover` | 3000 | 標配恢復公鑰成本 |
| 讀 nonce + code | 2600 | 冷讀帳戶 |
| 暖帳戶 SSTORE | 200 | 已熱就便宜 |
| 部署 23 bytes 指示碼 | 4600 | 200 × 23 |
| **總計** | **≈ 12 016** → 四捨五入成 12 500 | 定義成 `PER_AUTH_BASE_COST` |

> 為什麼要四捨五入？作者說「還要算搬來搬去雜支」，索性加到吉祥數字 12 500。

#### 7‑2 如何「洗掉」 delegation indicator？

* 把目標 address 設成 **0x0**。  
* 但第一次 call 還是要吃 2600 gas（冷讀 0x0 code），所以特別開後門：直接把 code hash 重設為空值，**真正回歸純 EOA**。

#### 7‑3 不封鎖 Storage／Create 指令的理由

* 如果禁止 `SSTORE`，Smart EOA 會超難用，要另開外部倉庫合約，麻煩又慢。  
* 對開發者來說，一套錢包跑兩條路線（SC Wallet vs. EOA Wallet）＝ 地獄維護。統一才是王道。

#### 7‑4 跨鏈安全：Chain ID 怎麼配？

* `chain_id = 0` → 不設防，全鏈都可用；  
* 指定值 → 只給那條鏈，像「國碼加手機號」一樣。  
* 有人提議「把 bytecode 也簽進去」，但要查鏈上 code，多一次資料庫 IO，傳播起來爆炸，就被否決。

#### 7‑5 為何 `EXTCODE*` 不跳 Delegation？

* 假如會跳，壞人可把自己偽裝成 Uniswap Router 的 code hash，合約驗證邏輯全破。  
* 現在只讓 `CALL*` 等執行指令跳；`EXTCODE*` 還是乖乖回傳 0xef0100… 指示碼本身。

#### 7‑6 Gas 先收後退：設計哲學

1. 檢查交易內在 gas → 直接用「最貴」算（假設帳戶是空的）。  
2. 真正處理 `authorization_list` 時，如果發現目標帳戶早就存在，就退你差額。  
3. 好處：**驗證速度快**，不需為每個 tuple 先查狀態再決定收多少。

#### 7‑7 為何不把 Blob / Contract Creation 一併塞進來？

* **Blob（EIP‑4844）** 需要額外 P2P 邏輯，增加節點頻寬；  
* **合約建立** = 新增 `initcode` 執行分支，測試組超痛。  
* 對作者來說：「我們只想改善 UX，就別一次包山包海。」

#### 7‑8 不讓 Delegation 指向 Precompile？

* Precompile 館裡面根本沒 code，執行跳進去也是空轉；  
* 與其搞例外處理，不如直接規定「指到 Precompile = 成功但啥也不做」。

#### 7‑9 Mempool 攻略：一次只收一筆

* Smart EOA 一個交易可以掃光自己 balance，讓其他掛單全變廢單。  
* 節點守則：一個「已委派」的 EOA，在 mempool 只能有一張排隊票，不再讓它疊羅漢。

#### 7‑10 安全 Tips

1. **Delegate 合約自己要很會保護**：  
   * Nonce 重放、防爆 gas、白名單目標合約… 缺一不可。  
2. **初始化前置搶跑**：  
   * 第一筆初始化 call 一定要 `ecrecover` 驗 EOA 簽名，別讓路人先搶設定。  
3. **Storage 遷移**：  
   * 用 ERC‑7201 slot 命名空間； or 先 `SSTORE` 清零再升級，確保不打架。  
4. **Sponsored 中繼者**：  
   * 先收押金／做信譽系統，免得被白嫖 gas。  

---

### 8. 整理

* 7702 像是給原本笨重的 EOA 加上「一次性外掛」。雖然不完美，但 win rate 超高。  
* 真要極致安全、玩模組化，4337 依舊是王道；7702 當成快速補丁，兩邊並行最好。  
* **行動呼籲**：  
  * 如果你是 DApp 前端：現在就開始在測試網打 `eth_sendTransaction(type: 0x04)`。  
  * 如果你做錢包：趕緊做 `authorization_list` 視覺化 + 白名單機制。  
  * 如果你是使用者：分叉之後先小額試水，不要一口氣把全部資產搬過去。

### 7702 適合誰？

- 想快速優化 DApp 流程的個人開發者
- 不想部署整套 4337 架構的早期項目
- 想降低新用戶 onboarding 門檻的產品團隊

### 2025.05.15

> 昨天（Day 1）我主要在「**為什麼要有 EIP-7702？**」這條脈絡裡打轉，算是先把動機和整體藍圖貼到腦海。  
> **Day 2** 則完全跳進 _Specification_ 的細節池，來一次「條文逐條念、自己動手畫序列圖、邊看邊碎念」的深蹲。  

1. **部署一段最小代理邏輯**：用 23 bytes 的萬用 Minimal Proxy，當作 7702 指向的目標。  
2. **組一筆 `type: 0x04` 交易**（也叫 *set-code tx*），把我的 Sepolia 測試錢包升級成「Smart EOA」。  
3. **看一下 On-chain 結果**：Nonce、Code Hash、Gas 用量、Mempool 行為。  
4. **寫下反思**：這條流程到底卡在哪裡？對開發者/使用者 UX 友不友善？

### 1. Set Code Transaction 逐條翻譯筆記

| 條文片段 | 我的白話註解 |
|----------|-------------|
| `SET_CODE_TX_TYPE = 0x04` | 0x01～0x03 都各有主流用處（0x01: Legacy, 0x02: 1559, 0x03: 4844 Blob），7702 剛好排到 0x04，抽象地說「這是一張能改自己程式碼的 VIP 票」。 |
| `authorization_list = [[chain_id, address, nonce, y_parity, r, s] ...]` | 「我（EOA 本人）同意，把我的 code 指到 **address** 這裡去。」後面那六欄就是一顆迷你交易簽名包：<br> • `chain_id` = 跨鏈重放開關<br> • `nonce` = replay guard + lockstep<br> • `(y_parity, r, s)` = 標準 secp256k1 簽名片段 |
| `0xef0100 || address` | 傳說中的 Delegation Indicator。`0xef` 本來就被 3541 禁用 → 看到就知道「這不是正常 bytecode，請特殊處理」。緊跟 `0x0100` 再貼 20 byte address，秀肌肉同時防誤解。 |
| 「交易無法 revert 已寫入的 delegation」 | 這句最辣。代表只要授權寫進去，就算後面那筆 call 失敗，delegation 還是黏在鏈上，_state transition_ 不回滾。寫錯地址 = 腦袋着火。 |

*直觀對比 4337：7702 把「validate + execute」兩步壓在同一層 TX，4337 則拆成 `UserOperation` 先排隊、再被 Bundler 打包。*

---

### 2. Gas 帳本 - 為何要收 12 500？

| 成本項 | 數字 | 我的 OSU! 彈幕 |
|-------|-----|----------------|
| 101 bytes Calldata | 1 616 gas | 「這個長度哪來？」→ 草案作者假設授權 tuple 大概就這麼大。 |
| `ecrecover` | 3 000 | 固定價。 |
| 冷讀 nonce + code | 2 600 | EIP-2929 規定。 |
| 暖帳戶 SSTORE | 200 | 因為剛剛已讀過帳戶，算 warm。 |
| 寫 23 bytes 指示碼 | 4 600 | 23 × 200，沒懸念。 |
| **合計** | **12 016** | 四捨五入 → 12 500，感覺作者想留 buffer 給雜支。 |

> **小心得**：`PER_EMPTY_ACCOUNT_COST = 25 000` → 如果原帳戶沒 code，更貴；如果本來就有 delegation，只算 `PER_AUTH_BASE_COST`。意味著「一直改來改去」會燒更多 gas，逼你慎選目標 code。

---

### 3. 為什麼 Delegation 不跳 Precompile？

* Precompile（例如 ecrecover(0x01)、sha256(0x02) ...）帳戶沒有 bytecode，跳進去只是一坨 C++。  
* 若允許 Delegation → 客戶端還得判斷「是 Precompile 就跑原生函式，還是去讀 address 的 bytecode？」顯示太多 if-else，不優。  
* 因此乾脆規定：**指到 Precompile 視同「空程式碼」**，call 成功但啥也不發生。乾淨俐落。

---

### 4. 五個我今天的「啊哈！」瞬間

1. **`msg.sender == tx.origin` 破功** → 老式反閃電貸 check 要汰換。  
2. **Clearing delegation** 不是把 code 設空，而是指到 0x0 且額外清 code hash，薄荷味十足。  
3. 照官方流程，**先寫 delegation → 再執行 calldata**；這跟我直覺「先跑，再 commit」顛倒！  
4. 若 `authorization_list` 炸了（簽名錯等），EVM 會 _skip tuple_ 但不 revert 整筆 TX；Meaning：可以附好幾個候補，像 HTTP 備援 domain。  
5. 交易 intrinsic gas 直接用 **最壞情況** 收錢，再 refund → 驗證期不用查 N 次狀態，Performance ++。

---

### 5. 疑惑清單 (To Ask)

1. 署名 tuple 裡的 `nonce` 條件：若 authority 已經有更高 nonce 會怎樣？  
2. Delegation Indicator 長度 23 bytes 怎麼算？（`0xef`+`0100`+20B addr = 23B，OK）但為何不用 `0xfe` 像 4337？  
3. 如果兩個 tuple 指到不同 address，最後一次覆蓋前一次，那前一次還要付 gas 嗎？（應該要）  
4. 節點「一次收一筆」策略是否會寫進共識或只是 best practice？  
5. 哪家錢包最快把 0x04 交易打到測試網？（要去觀察 Twitter）

### 2025.05.16
# 1 🔀 EIP-7702 vs ERC-4337 極端比較

| 維度 | **EIP-7702 (Smart EOA)** | **ERC-4337 (SC Wallet)** | 備註 |
|------|--------------------------|--------------------------|------|
| **上線方式** | 協議層硬分叉〈Pectra〉 | 合約層、已主網 | 7702 需升級 Client、共識 |
| **啟用門檻** | 只要錢包會簽 type 0x04 | 必須有 Bundler + EntryPoint | 7702 更輕量 |
| **交易流** | L1 直接一筆 tx | UserOp → Bundler → EntryPoint → L1 | 4337 多一跳 |
| **批次操作** | 一次 tx | Bundler 打包 | 都行 |
| **Gas 贊助** | 第三方可代付 | Paymaster | 4337 已有模組 |
| **Session Key / 子權限** | 可自行寫 Impl 處理 | Safe Module 現成 | 4337 成熟度高 |
| **私鑰停用/重啟** | 待 EIP-7851 | 直接換 Owner 模組 | 4337 完勝 |
| **`tx.origin` 破壞** | 會 (可能≠msg.sender) | 不會 | 老合約需審計 |
| **最優場景** | 空投、單次腳本、PoC | 全功能錢包、GameFi | 按需選擇 |

> **TL;DR**：7702 像「FOC 可攜帶鍵盤」，4337 是「高級機械鍵盤」。小事用 7702，大事上 4337。

---

# 2 🖼️ Mermaid — 雙流比較

```mermaid
sequenceDiagram
    autonumber
    participant U as User
    participant W as Wallet
    participant L1 as L1 EVM
    participant B as Bundler
    participant EP as EntryPoint

    %% --- 7702 Path ---
    rect rgb(230,255,230)
        U ->> W: 產生 type 0x04 TX\n(含 authorization_list)
        W ->> L1: eth_sendRawTransaction
        L1 ->> L1: [A] 處理 authorization\n    • ecrecover\n    • 寫 delegation
        L1 ->> L1: [B] 執行 calldata / value
        L1 -->> U: TX receipt
    end

    %% --- 4337 Path ---
    rect rgb(230,230,255)
        U ->> W: 產生 UserOperation
        W ->> B: eth_sendUserOperation
        B ->> EP: handleOps(UserOp)
        EP ->> L1: 執行\n(驗簽 + call target)
        L1 -->> EP: 完成
        EP -->> B: emit events
        B -->> U: UserOpHash + status
    end
```
### 2025.05.17

=================
#### Stress Test & 實戰 Session Key

#### 今天？
對 EIP-7702 做一輪「壓力測試」與「Session Key」場景驗證。因為前幾天已經掌握基本的 delegation 流程與 gas 成本，那今天的目標就是要往真實場景裡鑽，看看這個提案在極限情況下是否還站得住腳，以及當我們設計應用邏輯（尤其是與 session key 相關的細節）時，有沒有會被忽略的漏洞。

我從三個角度切入：
1. 重複 delegation：會不會被重複計費？先前設的權限會不會被覆蓋？
2. session key 的實際運作：我模擬一個使用者用 session key 授權第三方做一些動作（比如轉帳、投票），看看這些 key 的生命週期與效力邊界。
3. 惡意 calldata 攻擊：我刻意送進一堆超長、亂碼 calldata 看能不能 crash 掉整個流程（畢竟安全性也是一環）。

#### 觀察與發現

### Delegation 重複設定 OK
我用相同 EOA 設置兩次 delegation 給同一個合約，第一次寫入的 code 被第二次完整覆蓋，而且總共只花了一次「部署代碼」的 gas（約 12,500 gas），不會像某些設計那樣每筆都重新部署浪費錢。這點很加分，表示只要設一次就好，之後可以更新而不是重新來過。

#### Session Key 權限測試
這裡我做了一個小實驗：讓一個「假第三方」拿 session key 去執行我定義好的 action（比如：轉帳 0.01 ETH 到某個固定地址），再嘗試去執行我沒授權過的 function。結果符合預期，非授權函式直接 revert。這代表如果我一開始就把 function selector 限制好，其實安全性不錯。

#### 防禦惡意 calldata
我送進各種奇怪的 payload，例如過長亂碼、多層巢狀 function call、甚至組合 0x00 + 0xff 等邊緣 bytes，7702 沒有直接暴斃，但仍需要靠我的 smart contract 嚴格判斷 function selector，否則仍可能被某些合約邏輯誤打誤撞觸發。所以說，EIP 本身沒問題，關鍵還是在於我們怎麼「審核 call 的內容」。

#### 撤銷 revoke 成功
我設計一個 revoker function，只要被呼叫，所有先前的 session key 都會立即失效，模擬使用者點下「撤銷授權」按鈕後的效果。再次驗證 call，會馬上被 revert 掉。這表示可以實作「可控制 session 的臨時代理」。

#### 一些延伸心得

其實從今天的測試可以發現一件事：7702 雖然不像 4337 有一堆 bundler、paymaster 生態，但勝在「簡單直接可控」。這對於做小型應用、PoC（Proof of Concept）產品或教育教學環境來說超級實用。你只要有一個 EOA，就可以「裝備一次性的智慧功能」，比如限時轉帳、單日簽名有效等等，這些以前都要靠 full-featured smart wallet 實現，現在只用一次交易搞定。

而在安全性方面，它其實不提供額外保護，你還是得靠自己的 smart contract 寫好權限邊界。所以你怎麼切割權限、怎麼用 function selector 限定行為，這是成敗關鍵。

#### 結語：技術雖小，但用法千變萬化

今天的重點不在「玩很花的功能」，而是「驗證簡單功能是否可靠且安全」。我把 session key + delegation 想像成是一種「臨時授權合約」，然後嘗試做出一套像 Apple Shortcuts 那樣的操作流程。透過這些壓力測試，我相信這東西可以做得穩，也能簡單到讓前端開發者無痛接手。這才是真正「可用的技術」。

> 總結：  
> 如果 4337 是高鐵，7702 就是小電動。雖然不能飆 300 公里，但停車、轉彎、改方向都超靈活。  

---


### 2025.05.18

- **7702 是讓 EOA 有機會「在送交易的那一刻」指定一段合約邏輯，臨時讓 EVM 執行那段邏輯，然後就跟平常一樣繼續送交易**。
- 用一句話說：**「一次性的合約外掛」，在不換地址的前提下，讓你的帳號變聰明**。

這跟 4337 最大差別就是：
- 4337 要你轉資產進 smart wallet，然後用 bundler 幫你送交易；
- 7702 你什麼都不用轉，直接在原本的帳號用「一筆交易裝外掛」。

---

#### 有效期限內可用的帳號（Session Wallet）

使用場景：我今天要登入某個 DApp，網站跟我說：「你今天可以用這組 session key 做操作，但只到晚上 10 點。」

以前做法要用 4337 + paymaster + sponsor，設定一堆複雜規則；  
7702 我可以只用一個 EOA 加上一次性的 delegation 設定，寫明：
- 這組 key 有效到幾點
- 只能操作什麼 function
- 一旦時間到，自動失效

適合用在：
- DeFi 平台「限時操作權限」
- GameFi 登入驗證（像是日常任務打怪，只授權打怪功能）
- DAO 投票權限分離（暫時授權某人幫我投票）

---

#### 找別人幫我出錢（Gas Sponsor）

7702 也可以很自然地實現一種「代付 gas」的功能。

我原本的 EOA 沒錢，但我可以在交易裡加上：
- 指定誰可以幫我出 gas
- 出錢人條件（只能在某些 function 發生、或每天一次）

這在現實中超實用：
- 像新手註冊 NFT、參加空投，要上鏈但沒 ETH
- 公司內部有個主管錢包，幫所有員工支付每日 quota
- 教學活動中讓學生用 session key 測試交易，不怕亂花錢

---

#### 「簽一下就能做很多事」的 UX 改造

一般使用者對「交易」這件事很敏感。你叫他每一步都簽一次，他就很煩。

那我就可以用 7702 把他第一筆簽名裡的 delegation 設定好，寫明：
- 之後 30 分鐘內，你不用再簽名
- 系統可以自動幫你做 XX 操作（例如 mint、claim、roll、打卡）

這樣的 UX 非常適合：
- NFT 平台：簽一次就可以連續 mint 不同作品
- L2 批次操作：你不用每筆都簽、只用一次就可以 rollup
- 社交平台簽名：簽一次等於授權某人幫你自動回推文、轉發

---

##### 一次性授權別人用我的帳號（小心！）

這是我最感興趣的用途。

情境是這樣的：
> 我有一組帳號裡面放了資產，但我今天要出門旅行。  
> 我希望朋友幫我操作一次「幫我轉帳 0.1 ETH 給我媽」。

你敢給他私鑰嗎？當然不敢。  
但你可以：
- 用 7702 設定好 delegation：這個 EOA 可以做一次 call（function: transferETH）
- 限制只能轉到 0xMOM 的地址
- 一次執行完就不能再用了

這就像是「一次性密碼 + 限權限 + 自動失效」，超適合臨時交接、備援操作。

---

#### 想像未來：如果 7702 普及了會怎樣？

我覺得 EIP-7702 真的「做對了一件事」：**它沒有要求你改變整個系統的運作，只是加了一個小選項而已**。

就像 HTTP 加上 Cookie、或 Email 加上附件，簡單又強大。

想像一下如果它普及：

- 教學課程可以直接給學生一組 session key 讓他玩，不怕錢包被搞壞。
- DAO 可以讓成員 vote 的權限跟資產帳戶分開，安全又輕鬆。
- 小型應用可以做「免註冊、免轉帳」的快速互動功能。
- 驗證、打卡、限時活動都能自動授權、自動失效。

---

#### 結語：不是在想「我能寫什麼」，而是「我能改變什麼」

今天的重點，其實不在於寫出多少 code，而是學會換個角度去思考：

> 「如果這個功能被廣泛採用，我能讓哪件事變得更好？」

我覺得 7702 就是這樣一個提案——不是強迫你用，而是給你多一個選項。

你原本只是一個無聊的 EOA，現在你可以有 session key、有代付機制、有臨時權限管理，這改變了很多 Web3 的使用體驗。

如果我以後真的做一個產品，我一定會先想：
- 有沒有機會讓用戶只簽一次？
- 能不能讓帳號不用動資產也能變聰明？
- 是否能把「安全性」跟「操作性」拆開來設計？

這樣的設計邏輯，是我從 7702 學到最大的事。
<!-- Content_END -->
