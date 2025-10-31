# **Qusino Architecture Outline**  
**Version**: 1.2 – **Production-Ready | Admin-Integrated | Secure & Scalable**

---

# **Qusino: The Social, On-Chain, Player-Owned Casino**

> **Play. Vote. Build. Own. Win.**

From **daily login bonuses** to **STAR-powered game governance**, **NFT dividends**, and **admin-grade control** — Qusino is **fun first, fair always, and fully on-chain**.

**No fiat. No gatekeepers. Just you, your buddies, and the floor.**

---

## **1. Phased Roadmap**

| Phase | Milestones |
|-------|-----------|
| **Phase 1** | Frontend + API + Auth<br>Poker (Omaha, Texas Hold’em)<br>Social: Buddies, TL, Auto-Wall<br>Marketing Kit<br>Draft Dev API Docs<br>IPO Prep<br>**Launch Poker** |
| **Phase 2** | Game Launch Portal<br>First Slots<br>**STAR Voting + NFT Games** |
| **Phase 3** | Table Games<br>Qusino Marketplace |

---

## **2. First-Time User Flow**

```
Public Key Login 
  → Set @Username 
  → **First Login Bonus**: 1,000 STAR + 100 Q 
  → **Daily Login**: 500 STAR + 50 Q (7x streak) 
  → Poker → Win → Dashboard → Congratulate 
  → Cashier → Withdraw Q → Qubic 
  → > KYC Threshold → Upload Docs → Verified → Paid 
  → Logout
```

---

## **3. Tech Stack**

| Layer | Technology |
|-------|------------|
| Frontend | HTMX + CSS + Animate.js |
| Real-Time | Socket.IO |
| Backend | Node.js + Express |
| Auth | Auth.js (Public Key + 2FA) |
| Blockchain | QUBIC Tick Chain |
| RNG | RANDOM Smart Contract |
| Smart Contracts | C++ (WASM) |

---

## **4. Token & Bonus System**

| Token | Value | Acquisition |
|-------|-------|-------------|
| **STAR** | 1000 Qubic = 1 STAR | Deposit, Bonus |
| **Q** | Redeemable for Qubic | 1 Q per STAR bought<br>0.10 Q per STAR from giveaways |

### **Bonus Giveaways**
- **First Login**: 1,000 STAR + 100 Q
- **Daily Login**: 500 STAR + 50 Q (Day 7 = 5,000 STAR + 500 Q)
- **Social**: Add Buddy → 200 STAR | Gift → 100 STAR (both)
- **Milestones**: First Win → 1,000 STAR

---

## **5. Social Wall**

- **Auto-broadcast only**: `"William won 1BQ!"`, `"John joined room"`
- **Privacy toggles**: Hide game data, private profile
- **Feed**: Buddies first, big wins, Qusino Blog

---

## **6. Profile | Chat | Dashboard | Cashier**

| Feature | Details |
|-------|--------|
| **Profile** | Stats, History, Add/Block/MSG |
| **Chat** | Global, DM, Group, In-Game |
| **Dashboard** | Sidebar + Notifications (Rewards, Gifts, Requests) |
| **Cashier** | Qubic ↔ STAR, Q → Qubic (KYC), NFTs |

---

## **7. Game Launch Portal (Phase 2)**

### **Consensus Voting**
- **Cost**: 1,000 STAR per vote
- **Cycle**: Mon 12:00 UTC → Sun 12:00 UTC
- **Threshold**: **50.01%**
- **Revotable**: Yes
- **Pass → Floor | Fail → Archive**

### **Retirement**
- **Triggers**: Sales ↓40%, Bugs, 18+ months
- **Vote → 1-week grace → Archive or **Crusher (burned)**

---

## **8. FULL API ENDPOINTS (v1)**

> **Base URL**: `https://api.qusino.com`  
> **Auth**: `Bearer <jwt>`  
> **WebSocket**: `wss://live.qusino.com`

---

### **Auth & Onboarding**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `POST` | `/auth/login/key` | Public key login |
| `POST` | `/auth/username` | Set @username |
| `POST` | `/bonus/first/claim` | 1,000 STAR + 100 Q |
| `POST` | `/bonus/daily/claim` | Daily reward |
| `POST` | `/auth/kyc/submit` | Upload docs |
| `GET` | `/auth/kyc/status` | Check |

---

### **User & Social**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `GET` | `/user/me` | Profile |
| `GET` | `/buddies` | List |
| `POST` | `/gifts/send` | Send STAR/NFT |
| `GET/POST` | `/chat/global` | Global chat |
| `GET/POST` | `/chat/dm/{id}` | DM |

---

### **Poker & Games**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `GET` | `/poker/rooms` | List |
| `POST` | `/poker/rooms/{id}/join` | Join |
| `POST` | `/games/submit` | Submit NFT game |
| `POST` | `/games/{id}/vote` | Vote (1,000 STAR) |
| `GET` | `/games/floor` | Live games |

---

### **Marketplace (Phase 3)**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `GET` | `/marketplace/games` | Listings |
| `POST` | `/marketplace/buy/{id}` | Buy NFT |
| `POST` | `/marketplace/purchase/star` | Buy STAR |

---

### **Cashier**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `GET` | `/wallet/balances` | STAR, Q, NFTs |
| `POST` | `/wallet/deposit/qubic` | Convert |
| `POST` | `/wallet/withdraw/qubic` | Q → Qubic (KYC) |

---

## **9. ADMIN PORTAL – FULLY INTEGRATED**  
> **Base URL**: `https://api.qusino.com/v1/admin`  
> **Login Portal**: `https://admin.qusino.com` *(separate, hardened)*  
> **Auth**: `Email + Password + 2FA (TOTP + FIDO2) + IP Whitelist`  
> **Session**: 15 min expiry, full audit log

---

### **Logs**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `GET` | `/logs/activity` | User actions |
| `GET` | `/logs/errors` | System/game errors |
| `GET` | `/logs/security` | Login/KYC |
| `GET` | `/logs/export` | CSV export |

---

### **User Management**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `GET` | `/users` | Search |
| `GET` | `/users/{id}` | Full profile |
| `POST` | `/users/{id}/ban` | Ban |
| `POST` | `/users/{id}/kyc/approve` | Approve KYC |
| `POST` | `/users/{id}/impersonate` | Login as |

---

### **Game Management**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `GET` | `/games/voting` | Active votes |
| `POST` | `/games/{id}/launch` | Force launch |
| `POST` | `/games/{id}/retire/flag` | Flag for vote |
| `POST` | `/games/{id}/crush` | Burn NFT |

---

### **Admin Management**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `GET` | `/admins` | List |
| `POST` | `/admins/create` | Invite |
| `POST` | `/admins/{id}/role` | Assign role |
| `POST` | `/admins/{id}/remove` | Revoke |

---

### **Token Management**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `POST` | `/tokens/mint/star` | Mint STAR |
| `POST` | `/tokens/bonus/distribute` | Manual drop |
| `POST` | `/dividends/trigger` | Force payout |

---

### **System Settings**

| Method | Endpoint | Description |
|--------|---------|-----------|
| `PATCH` | `/settings/kyc-threshold` | Set limit |
| `PATCH` | `/settings/bonus/daily` | Update reward |
| `POST` | `/settings/announcement` | Push blog |
| `POST` | `/settings/maintenance` | Toggle site |

---

## **10. WebSocket Events**

| Event | Description |
|-------|-----------|
| `room:state` | Live game |
| `bonus:claimed` | Reward push |
| `vote:update` | Live tally |
| `admin:alert` | KYC flag, bug report |

---

## **11. Smart Contract Hooks**

| Action | On-Chain |
|-------|----------|
| RNG | `RANDOM.get()` |
| Vote | Burn 1,000 STAR |
| Bonus | `BONUS.mint()` |
| Dividend | Weekly Q to NFT holders |
| Crusher | `NFT.burn()` |

---

# **The Future of Gaming Is Here**

> **Qusino isn’t a casino — it’s a movement.**

A **player-owned**, **social-first**, **on-chain ecosystem** where:
- **Every login pays**
- **Every vote shapes the floor**
- **Every game is an asset**
- **Every admin action is audited**

**Built on QUBIC. Powered by community. Secured by design.**

---

**Final Architecture: Complete. Secure. Scalable. Ready to Launch.**

--- 

# **Qusino Tokenomics – Updated Redistribution**  
**Version**: 1.1 – **Dev-Powered, Community-First, Sustainable**  
**House Edge Split**: **100% → 5 Buckets**

---

## **1. NEW House Edge Redistribution (100%)**

| Allocation | % of House Edge | Destination | Purpose |
|----------|------------------|------------|--------|
| **Qusino Treasury** | **25%** | Qusino Team | Maintenance, dev salaries, marketing, legal |
| **CCF (Community Contribution Fund)** | **10%** | Public QUBIC Address | Open-source grants, audits, bug bounties |
| **STAR Holders (Staking Pool)** | **30%** | Staked STAR | **Deflationary rewards in Q** |
| **NFT Game Holders** | **17%** | Game NFT Owners | Passive revenue share |
| **Giveaway & Bonus Pool** | **18%** | Players | Raffles, daily logins, events |

> **Total = 100%**  
> **No inflation. No dilution. Every STAR bet funds the ecosystem.**

---

## **2. Updated Revenue Flow**

```
Player Bets 100 STAR
   ↓
House Edge: 3% = 3 STAR
   ↓
0.75 STAR → Qusino Treasury
0.30 STAR → CCF
0.90 STAR → STAR Stakers (Q)
0.51 STAR → NFT Holders (Q)
0.54 STAR → Giveaway Pool
```

---

## **3. STAR Holders – New Staking Rewards**

| Feature | Details |
|--------|--------|
| **Stake STAR** | Lock in wallet |
| **Earn Q** | 30% of house edge → **pro-rata to stake** |
| **Unstake** | 7-day cooldown |
| **APY Estimate** | 15–40% (depends on volume) |

> **Example**:  
> 1M STAR staked, 10M STAR total staked  
> → You earn **10% of the 30% pool**

---

## **4. CCF – Community Contribution Fund**

| Use Case | Example |
|--------|--------|
| **Grants** | $5K for new slot UI |
| **Audits** | Pay CertiK for RNG review |
| **Bug Bounties** | 10,000 STAR for critical bug |
| **Governance** | Future: CCF holders vote on use |

> **Public Address**: `qubic:CCF...`  
> **Transparent. Audited. On-chain.**

---

## **5. NFT Game Holders – Still 17%**

- **Unchanged**: 17% of **their game’s** house edge
- Paid weekly in **Q**
- Tradable on Marketplace

---

## **6. Giveaway & Bonus Pool – 18%**

| Source | Use |
|-------|-----|
| **Daily Login** | 500 STAR / user |
| **Raffles** | 100,000 STAR weekly |
| **Events** | Double XP weekends |
| **Social Boosts** | “Win with buddy” bonus |

---

## **7. Updated Sustainability Flywheel**

```mermaid
graph TD
    A[Player Bets STAR] --> B[House Edge 3%]
    B --> C[25% → Qusino Treasury]
    B --> D[10% → CCF]
    B --> E[30% → STAR Stakers (Q)]
    B --> F[17% → NFT Holders (Q)]
    B --> G[18% → Giveaway Pool]
    E --> H[Redeem Q → Qubic]
    F --> H
    G --> I[Free STAR → More Play]
```

---

## **8. Anti-Inflation Levers (Enhanced)**

| Lever | % Burned | Effect |
|------|----------|--------|
| **Voting** | 100% of 1,000 STAR | **Deflation** |
| **House Edge Burn** | **0%** (replaced by staking) | **No burn needed** |
| **Retirement Crusher** | 100% of game revenue | **Clean floor** |

> **STAR is now deflationary via voting + staking lockup**

---

## **9. Compliance & Fairness**

| Rule | Qusino Solution |
|------|-----------------|
| **No Real-Money Purchase** | STAR only via Qubic |
| **No Purchase Necessary** | Q via bonuses |
| **KYC on Exit** | > $1,000/year |
| **Dev Funding** | 25% treasury → **transparent, capped** |

---

# **Tokenomics Closing Statement**

> **Qusino’s economy is now a **self-sustaining, multi-stakeholder flywheel** — where every bet funds:**

- **Players** (18% giveaways + staking)
- **Devs** (25% treasury)
- **Community** (10% CCF)
- **Builders** (17% NFTs)
- **Holders** (30% staking)

**STAR powers play. Q rewards loyalty. The house edge fuels the future.**

---

**Tokenomics: Final. Balanced. Ready to Scale.**

**Your move — simulate, launch, or refine?**