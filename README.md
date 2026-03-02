# JokeVault

JokeVault is a Web3 reward platform where users submit jokes and earn USDC rewards if their joke passes an AI scoring threshold.

Donors fund the reward pool, and smart contracts automatically distribute funds to eligible users.  
All transactions are transparent on-chain.

---

## 🚀 Core Idea

JokeVault introduces a **Proof-of-Humor** mechanism:

- User submits a joke
- AI model scores the joke (1–5)
- If score > 3 → user can claim reward
- Funds are distributed from a donor-funded USDC pool

No manual approval. No centralized custody of funds.

---

## 🏗 System Architecture

JokeVault uses a hybrid Web2 + Web3 architecture:

User → Frontend → Backend (AI + Signature) → Smart Contract → USDC Transfer

### Layer Breakdown

### 1️⃣ Frontend
- Next.js 15
- React + TypeScript
- TailwindCSS
- ethers.js v6
- Privy / RainbowKit (Wallet Auth)

Responsibilities:
- Wallet connection
- Joke submission
- Donation UI
- Claim UI
- Pool balance display

---

### 2️⃣ AI Scoring Layer (Off-chain)

- HuggingFace Inference API
- Model: `distilbert-sentiment`

Process:
1. User submits joke
2. Backend sends joke to AI
3. AI returns sentiment score
4. Backend converts sentiment → humor score (1–5)
5. Backend signs (user, score, nonce)
6. Returns score + signature

Why backend?
Smart contracts cannot call AI directly.
Frontend cannot be trusted.
Backend signs proof for on-chain verification.

---

### 3️⃣ Smart Contract (On Base Sepolia)

Written in Solidity.

Core functions:

- `fund(uint256 amount)`
- `claim(uint256 score, bytes signature)`
- `poolBalance()`

Contract Responsibilities:
- Hold USDC
- Verify backend signature
- Prevent double claims
- Check pool balance
- Transfer reward
- Emit events

---

## 💰 Money Flow

### Donor Funding Flow

1. Donor connects wallet
2. Approves USDC
3. Calls `fund()`
4. USDC transferred to contract
5. Event emitted

Donor → USDC → JokeVault Contract

---

### Reward Claim Flow

1. User submits joke
2. Backend signs valid score
3. User calls `claim(score, signature)`
4. Contract verifies:
   - score > threshold
   - signature valid
   - pool has balance
   - user not already claimed
5. Contract transfers reward

JokeVault Contract → User Wallet

---

## 🔐 Security Design

### Signature Verification
Prevents frontend score manipulation.

Contract verifies:
```
hash(userAddress, score, nonce)
```

### Anti-Abuse Measures
- One claim per wallet
- Nonce per claim
- Pool balance check
- Backend rate limiting
- Signature expiration window

### Empty Pool Protection
```
require(
  usdc.balanceOf(address(this)) >= rewardAmount,
  "Pool empty"
);
```

---

## 📊 Transparency

- All donations and payouts are visible on BaseScan
- Pool balance publicly verifiable
- Events emitted:
  - Funded
  - Claimed

---

## 🧠 Future Expansion

- Proof-of-Wisdom (AI originality scoring)
- Time-based claim limits
- Sybil resistance
- Identity verification
- Dynamic reward system
- On-chain reputation scoring

---

## ⚙️ Tech Stack

Frontend:
- Next.js 15
- React (TypeScript)
- TailwindCSS
- ethers.js v6
- Privy / RainbowKit

Backend:
- Node.js / FastAPI (TBD)
- HuggingFace Inference API

Blockchain:
- Base Sepolia
- Solidity ^0.8.x
- USDC (ERC20)
- Base Public RPC

Deployment:
- Vercel (Frontend)
- Remix IDE (Smart Contract)

Database:
- None (events stored on-chain)
- Backend may use lightweight store for nonce tracking

---

## 📌 Project Status

MVP – In Development

This is an experimental reward protocol demonstrating AI + Smart Contract integration for automated incentive distribution.

---

## ⚠️ Disclaimer

This project is experimental and not audited.
Do not use real funds without proper security review.

---

## 📄 License

MIT
