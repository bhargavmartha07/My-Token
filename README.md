# 🪙 MyToken (MTK) - An ERC-20 Implementation

This repository contains the smart contract for **MyToken (MTK)**, an ERC-20 compliant digital asset.

---

## 💡 ERC-20 Token Standard Explained

The **ERC-20** (Ethereum Request for Comments 20) standard defines a common set of rules for tokens on the Ethereum blockchain. These rules ensure that all tokens following the standard can interact reliably with decentralized applications (DApps), wallets, and exchanges.

The primary functions defined by the standard relate to **transferring tokens** and **allowing third parties to spend tokens** on the owner's behalf (allowances).

---

## ⚙️ Token Specifications

| Parameter | Value |
| :--- | :--- |
| **Token Name** | MyToken |
| **Token Symbol** | MTK |
| **Decimals** | 18 |
| **Total Supply** | 1,000,000 MTK (equivalent to $1,000,000 \times 10^{18}$ with decimals) |

The total supply is set at 1 million tokens, with a standard $10^{18}$ decimal precision, similar to Ether.

---

## ✨ Implemented Features

This ERC-20 contract includes the core functionality as required by the standard:

* **State Variables:** Mappings for balances and allowances.
* **Core Functions:** `transfer`, `transferFrom`, `approve`, `balanceOf`, and `allowance`.
* **Events:** `Transfer` and `Approval` events are emitted upon successful execution of token transfers and allowance changes, respectively.
* **Initial Minting:** All tokens are minted to the contract deployer upon deployment.

---

## 🚀 Deployment Instructions with Remix IDE

Remix is an excellent online integrated development environment (IDE) for compiling and deploying Solidity contracts.

1.  **Open Remix:** Navigate to the [Remix IDE website](https://remix.ethereum.org/).
2.  **Create File:** Create a new file (e.g., `MyToken.sol`) and paste the contract code.
3.  **Compile:**
    * Go to the **Solidity Compiler** tab (left sidebar).
    * Ensure the **Compiler** version matches the pragma in your contract (e.g., `0.8.x`).
    * Click the **Compile** button.
4.  **Deploy:**
    * Go to the **Deploy & Run Transactions** tab.
    * Select your **Environment** (e.g., `Injected Provider - Metamask` for a testnet, or a `JavaScript VM` for local testing).
    * Select the correct contract from the **Contract** dropdown (e.g., `MyToken`).
    * Click **Deploy**.
    * Confirm the transaction in your wallet (if deploying to a public network).
    * Once confirmed, the contract will appear under the **Deployed Contracts** section.



---

## 📖 Usage Examples

Assuming the token contract is successfully deployed and accessible via the Remix interface, here is how you interact with the core ERC-20 functions:

### 1. `balanceOf(address)`

* **Purpose:** To query the token holdings of any specific address.
* **Example:** Inputting the contract deployer's address will return the full `Total Supply` (e.g., a large number representing 1,000,000 MTK with 18 decimals).

### 2. `transfer(address recipient, uint256 amount)`

* **Purpose:** The standard method for an account owner to send tokens from their own balance to another address.
* **Example:** The current caller executes `transfer` with `recipient: 0x...BEEF` and `amount: 100`.
* **Result:** $100$ MTK is deducted from the sender's balance and credited to `0x...BEEF`. A `Transfer` event is logged.

### 3. `approve(address spender, uint256 amount)`

* **Purpose:** To grant a third-party address (the **spender**) permission to withdraw a specific amount of tokens from the caller's balance.
* **Example:** The caller executes `approve` with `spender: 0x...CAFE` and `amount: 500`.
* **Result:** The allowance for `0x...CAFE` to spend on behalf of the caller is set to $500$ MTK. An `Approval` event is logged.

### 4. `allowance(address owner, address spender)`

* **Purpose:** A view function to check how many tokens the `spender` is currently permitted to withdraw from the `owner`.
* **Example:** Inputting the caller's address as `owner` and `0x...CAFE` as `spender`.
* **Result:** Returns the approved amount (e.g., $500$ if the previous `approve` step was successful).

### 5. `transferFrom(address sender, address recipient, uint256 amount)`

* **Purpose:** Executed by the **spender** (the third party) to move tokens from the **sender's** balance to the **recipient**, using their pre-approved allowance.
* **Example:** The approved address (`0x...CAFE`) executes `transferFrom` with `sender: caller's address`, `recipient: 0x...C0DE`, and `amount: 100`.
* **Result:** $100$ MTK moves from the sender's balance to `0x...C0DE`. The allowance for `0x...CAFE` is automatically reduced by $100$. A `Transfer` event is logged.

---

## ✅ Testing Scenarios and Results

The following test cases were executed in a **Remix JavaScript VM** environment:

1.  **Initial State Check:**
    * **Action:** Called `balanceOf` for the deployer.
    * **Result:** Correctly returned the `Total Supply`.
2.  **Basic Transfer:**
    * **Action:** Called `transfer(Account 2, 500)`.
    * **Result:** Transaction successful. Account 1 balance decreased by $500$, Account 2 balance increased by $500$. `Transfer` event correctly emitted.
3.  **Transfer Insufficient Balance:**
    * **Action:** Account 2 tried to `transfer` more tokens than its balance.
    * **Result:** Transaction **reverted** (failed), ensuring balance integrity.
4.  **Approve and TransferFrom:**
    * **Action 1 (Account 1):** Called `approve(Account 3, 200)`.
    * **Result 1:** `allowance(Account 1, Account 3)` returned $200$. `Approval` event emitted.
    * **Action 2 (Account 3):** Called `transferFrom(Account 1, Account 4, 150)`.
    * **Result 2:** Transaction successful. Account 1 balance decreased by $150$. Account 4 balance increased by $150$. `allowance` for Account 3 decreased to $50$. `Transfer` event emitted.
5.  **Exceeding Allowance with TransferFrom:**
    * **Action (Account 3):** Tried to call `transferFrom(Account 1, Account 4, 100)` (only $50$ allowance remaining).
    * **Result:** Transaction **reverted**, successfully enforcing the allowance limit.

---

## 🧠 Lessons Learned

Building this ERC-20 token implementation provided valuable insights into smart contract development:

* **Understanding the Standard:** Gained a deeper appreciation for the strict requirements of the ERC-20 interface, particularly the necessity of emitting specific **Events** (`Transfer` and `Approval`) for off-chain applications to reliably track state changes.
* **Importance of `require()`:** Reinforced the critical role of the `require()` function for input validation and state checks (e.g., ensuring sufficient balance or allowance) to prevent unauthorized or unintended state transitions.
* **Allowance Mechanism:** Clarified the relationship between `approve` and `transferFrom`, which is fundamental to DApps like decentralized exchanges that need permission to move user tokens.
* **Handling Decimals:** Understood why tokens must handle amounts using the base unit (the amount $\times 10^{\text{decimals}}$) to prevent floating-point errors and maintain precision.