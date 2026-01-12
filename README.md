# UUPS Upgradeable Token Vault Protocol

This repository contains a **production-grade upgradeable smart contract system** implementing a **Token Vault protocol** using the **UUPS (Universal Upgradeable Proxy Standard)** pattern.  
The project is designed to reflect **real-world DeFi upgrade practices**, focusing on **security, storage safety, controlled upgrades, and long-term protocol evolution**.

Unlike simple upgrade demos, this system walks through a **complete upgrade lifecycle** from Version 1 to Version 3 while preserving all on-chain state and enforcing strict access control.

---

## What This Project Demonstrates

This project was built to showcase how professional blockchain protocols handle upgrades safely:

- Secure **UUPS proxy architecture**
- Safe **storage layout management** across versions
- **Initializer protection** to prevent takeover attacks
- **Role-based access control** for upgrades and admin actions
- Backward-compatible feature additions
- Cross-version state preservation

The design closely follows upgrade patterns used in large-scale protocols such as Aave, Compound, and Uniswap.

---

## Protocol Evolution

The Token Vault evolves through **three carefully designed versions**:

### 🔹 Version 1 – Core Vault

- Token deposits and withdrawals
- Deposit fee deduction
- User balance tracking
- Total deposit accounting
- Upgrade-safe initialization

This version establishes the **base storage layout** that all future versions must respect.

---

### 🔹 Version 2 – Yield & Controls

Built directly on top of V1 without breaking state:

- Configurable annual yield rate (basis points)
- Linear yield accrual (non-compounding)
- Yield claiming per user
- Deposit pause and unpause mechanism
- Dedicated `PAUSER_ROLE`

All V1 balances and totals remain untouched after upgrade.

---

### 🔹 Version 3 – Withdrawal Safety

Adds advanced safety mechanisms while preserving all prior data:

- Withdrawal request system
- Mandatory withdrawal delay
- Time-based execution enforcement
- Emergency withdrawal option
- One active withdrawal request per user

This version models real-world safety controls used during high-risk situations.

---

## 📁 Repository Structure

```
uups-token-vault/
├── contracts/
│   ├── TokenVaultV1.sol
│   ├── TokenVaultV2.sol
│   ├── TokenVaultV3.sol
│   └── mocks/
│       └── MockERC20.sol
├── test/
│   ├── TokenVaultV1.test.js
│   ├── upgrade-v1-to-v2.test.js
│   ├── upgrade-v2-to-v3.test.js
│   └── security.test.js
├── scripts/
│   ├── deploy-v1.js
│   ├── upgrade-to-v2.js
│   └── upgrade-to-v3.js
├── hardhat.config.js
├── package.json
├── submission.yml
└── README.md
```

Each directory reflects a **production workflow**: contracts, upgrades, security tests, and automated deployment scripts.

---

## Setup Instructions

### Prerequisites

- Node.js **v18 or v20**
- npm
- Git

### Install Dependencies

```bash
npm install
```

---

## Compile Contracts

```bash
npx hardhat compile
```

Contracts are compiled using modern Solidity standards and OpenZeppelin upgrade-safe libraries.

---

## Testing

```bash
npx hardhat test
```

### Test Coverage Includes

- Contract functionality per version
- Upgrade integrity (V1 → V2 → V3)
- Access control enforcement
- Initialization protection
- Storage layout safety
- Unauthorized upgrade prevention

The tests simulate **real upgrade flows** instead of isolated unit testing.

---

## Deployment & Upgrade Flow

### Start Local Network

```bash
npx hardhat node
```

### Deploy Version 1 (Proxy)

```bash
npx hardhat run scripts/deploy-v1.js --network localhost
```

### Upgrade to Version 2

```bash
npx hardhat run scripts/upgrade-to-v2.js --network localhost
```

### Upgrade to Version 3

```bash
npx hardhat run scripts/upgrade-to-v3.js --network localhost
```

>  The proxy address remains unchanged throughout all upgrades.

---

##  Storage Layout Safety

To avoid storage collisions:

- No variable reordering across versions
- New variables are **only appended**
- Storage gaps (`__gap`) reserved for future use
- Each upgrade reduces the gap size accordingly

Upgrade safety is validated using Hardhat Upgrades utilities before execution.

---

##  Initialization Protection

All implementation contracts:

- Use OpenZeppelin `initializer` / `reinitializer`
- Disable direct initialization via constructor

```solidity
constructor() {
    _disableInitializers();
}
```

This prevents attackers from initializing implementation contracts directly.

---

## Access Control Model

The protocol uses **AccessControlUpgradeable**.

### Roles

- **DEFAULT_ADMIN_ROLE**
  - Role management authority
- **UPGRADER_ROLE**
  - Controls implementation upgrades
- **PAUSER_ROLE**
  - Manages deposit pausing (V2+)

Upgrade authorization is strictly enforced:

```solidity
function _authorizeUpgrade(address)
    internal
    override
    onlyRole(UPGRADER_ROLE)
{}
```

---

## Design Choices & Scope

- Yield is linear and non-compounding
- Emergency withdrawals bypass delay intentionally
- No token slashing or penalties
- Assumes ERC20 compliance

These decisions prioritize **upgrade correctness and security clarity**.

---

## Automated Evaluation

The `submission.yml` file defines:

- Dependency installation
- Compilation commands
- Test execution steps

This enables **hands-free evaluation** by automated graders.
