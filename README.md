# Qusino Dual Model Architecture Outline

**Version**: 1.3 – Dual-Compliant | Geo-Routed | Global & US-Optimized

---

# Qusino: The Social, On-Chain, Player-Owned Casino (Dual Model)

**Play. Vote. Build. Own. Win.**

Qusino operates as a **dual-model platform** to ensure global accessibility and regulatory compliance:

- **Qusino.com (International Real-Money Casino)**: Licensed under the Isle of Man Gambling Supervision Commission (OGRA Act 2001). Offers real-money wagering via QUBIC blockchain for players outside restricted jurisdictions. Supports all games with fiat/crypto deposits, KYC-mandated withdrawals, and full on-chain fairness.
- **Qusino.us (US Social Sweepstakes Casino)**: Operates under US sweepstakes laws ("no purchase necessary" model). Uses **STAR** (fun play) and **Qs** (prize-eligible). Legal in 48 states (excl. ID, WA); redeem **Qs** winnings for cash/gifts post-verification. No real-money deposits—focus on free entry, bonuses, and social engagement.

**Geo-Routing**: Automatic IP-based redirection (US → Qusino.us; non-US → Qusino.com). VPN bypasses trigger compliance warnings. Shared backend for cross-model data sync (profiles, buddies, NFTs), with segregated wallets/tokens per model.

From daily login bonuses to **STAR/Qs-powered game governance**, NFT dividends, and admin-grade control — Qusino is fun first, fair always, and **fully on-chain** (where applicable).

**No gatekeepers. Just you, your buddies, and the floor.**

---

## Dual Model Overview

| Model | Domain | Jurisdiction/License | Currency Model | Key Features | Exclusions |
|-------|--------|----------------------|----------------|--------------|------------|
| **Real-Money (International)** | Qusino.com | Isle of Man (OGRA) – Covers all online gambling (casino, poker, betting; 5-year license, 0-1.5% tax on net profit) | QUBIC-native: **STAR** (utility), **Q** (redeemable 1:1) | Real stakes, crypto/fiat deposits, on-chain RNG/dividends | US, restricted territories |
| **Sweepstakes (US Social)** | Qusino.us | US Federal/State Sweepstakes Laws (no-purchase-necessary; compliant in 48 states) | Dual Virtual: **STAR** (fun-only, purchasable); **Qs** (prize-eligible, free via bonuses/mail-in) | Free play + cash redemptions (min. 100 **Qs**), social focus, no real-money wagering | ID, WA; real-money features |

**Compliance Hooks**:
- **KYC/AML**: Mandatory for redemptions >$2,000 (both models); Isle of Man standards for .com.
- **Age Gates**: 18+ (.us); 19+/21+ per state where stricter.
- **RNG**: On-chain `RANDOM.get()` for both; provably fair audits.
- **Shared Social Layer**: Buddies, chat, wall sync across models (geo-locked games).

---

## Phased Roadmap

| Phase | Milestones (Shared) | Real-Money (.com) | Sweepstakes (.us) |
|-------|---------------------|-------------------|--------------------|
| **Phase 1** | Frontend + API + Auth<br>Poker (Omaha, Texas Hold’em)<br>Social: Buddies, TL, Auto-Wall<br>Marketing Kit<br>Draft Dev API Docs<br>IPO Prep<br>**Launch Poker** | Real-stakes tables; QUBIC deposits | **STAR/Qs** play; free-entry promos |
| **Phase 2** | Game Launch Portal<br>First Slots<br>**STAR/Qs Voting + NFT Games** | On-chain voting (**STAR** burns) | Community voting (**Qs** entries); NFT prizes |
| **Phase 3** | Table Games<br>Qusino Marketplace | Real-NFT trading; dividends in **Q** | Virtual NFTs; redeemable prize packs |

**Dual Launch**: Qusino.com Q4 2025 (post-Isle license); Qusino.us Q1 2026 (US compliance audit).

---

## First-Time User Flow

**Geo-Routed Entry**:
- **Non-US (Qusino.com)**:
  ```
  Public Key/Fiat Login → Set @Username → **First Login Bonus**: 1,000 STAR + 100 Q → **Daily Login**: 500 STAR + 50 Q (7x streak) → Poker → Win → Dashboard → Congratulate → Cashier → Withdraw Q → QUBIC/Fiat (KYC) → > Threshold → Upload Docs → Verified → Paid → Logout
  ```
- **US (Qusino.us)**:
  ```
  Free Account Signup → Set @Username → **First Login Bonus**: 500K STAR + 50 Qs → **Daily Login**: 250K STAR + 25 Qs (7x streak) → Poker (STAR mode) → Switch to Qs → Win → Dashboard → Redeem Qs → Mail-In Option → Verified (ID check) → Cash/Gift → Logout
  ```

**No-Purchase-Necessary**: US users get free **Qs** via daily bonuses, social shares, or mail-in requests (e.g., postcard for 100 **Qs**).

---

## Tech Stack

| Layer | Technology | Dual Notes |
|-------|------------|------------|
| Frontend | HTMX + CSS + Animate.js | Responsive; geo-redirect via Cloudflare Workers |
| Real-Time | Socket.IO | Shared chat; model-segregated game rooms |
| Backend | Node.js + Express | Segregated APIs (.com vs .us); shared user DB |
| Auth | Auth.js (Public Key + 2FA) | .com: Crypto wallets; .us: Email/SMS + state ID |
| Blockchain | **QUBIC Tick Chain** | .com only; .us: Off-chain virtual ledgers w/ RNG hooks |
| RNG | `RANDOM` Smart Contract | On-chain for .com; certified PRNG for .us (auditable) |
| Smart Contracts | C++ (WASM) | .com: Full; .us: Hooks for prize draws only |

**Infrastructure**: AWS/GCP hybrid; CDNs for low-latency geo-routing.

---

## Token & Bonus System

| Model | Token/Currency | Value/Conversion | Acquisition |
|-------|----------------|------------------|-------------|
| **Real-Money (.com)** | **STAR** | `1 STAR = 1,000 QUBIC` | Deposit, Bonus, Marketplace |
| | **Q** | Redeemable for QUBIC (1:1 post-KYC) | 1 Q per STAR bought; 0.10 Q from giveaways |
| **Sweepstakes (.us)** | **STAR** | Fun-only (no redemption) | Purchases (entertainment packs), daily free |
| | **Qs** | Redeemable for cash/gifts (1:1, min. 100) | Free: Bonuses, mail-in, social; bonus w/ STAR buys |

### Bonus Giveaways (Model-Specific)

- **Real-Money (.com)**:
  - **First Login**: `1,000 STAR + 100 Q`
  - **Daily Login**: `500 STAR + 50 Q` → **Day 7 = 5,000 STAR + 500 Q**
  - **Social**: Add Buddy → `200 STAR` | Gift → `100 STAR` (both)
  - **Milestones**: First Win → `1,000 STAR`
- **Sweepstakes (.us)**:
  - **First Login**: `500K STAR + 50 Qs`
  - **Daily Login**: `250K STAR + 25 Qs` → **Day 7 = 2.5M STAR + 250 Qs**
  - **Social**: Add Buddy → `100K STAR + 10 Qs` | Gift → `50K STAR + 5 Qs` (both)
  - **Milestones**: First Win → `500K STAR + 50 Qs`

> .com: All bonuses minted via `BONUS.mint()` on-chain. .us: Virtual mints; compliant w/ free-entry rules.

---

## Social Wall

- **Auto-broadcast only**: `"William won 1BQ!"` (.com) / `"John hit jackpot in Qs!"` (.us)
- **Privacy toggles**: Hide game data, private profile
- **Feed**: Buddies first → big wins → Qusino Blog (shared; geo-flagged content)

**Cross-Model Sync**: US users see .com teasers (e.g., "Global wins"); non-US see .us social highlights.

---

## Profile | Chat | Dashboard | Cashier

| Feature | Details (.com) | Details (.us) |
|---------|----------------|----------------|
| **Profile** | Stats, History, Add/Block/MSG | Stats, History, Add/Block/MSG (virtual wins) |
| **Chat** | Global, DM, Group, In-Game | Global, DM, Group, In-Game (shared) |
| **Dashboard** | Sidebar + Notifications (Rewards, Gifts, Requests) | Sidebar + Notifications (Bonuses, **Qs**, Entries) |
| **Cashier** | QUBIC ↔ STAR, Q → QUBIC/Fiat (KYC), NFTs | **STAR/Qs** balances; Redeem **Qs** → Cash/Gift (ID verify); Mail-in portal |

---

## Game Launch Portal (Phase 2)

### Consensus Voting

- **Cost**: `1,000 STAR` (.com) / `50 Qs` (.us) burned/entered per vote
- **Cycle**: Mon 12:00 UTC → Sun 12:00 UTC
- **Threshold**: `50.01%`
- **Revotable**: Yes
- **Pass** → Floor | **Fail** → Archive

### Retirement

- **Triggers**: Sales ↓40%, Bugs, 18+ months
- **Process**: Vote → 1-week grace → Archive or **Crusher** (`NFT.burn()` .com; virtual burn .us)

**Dual Governance**: Shared votes influence both models; US votes weighted for social features.

---

## FULL API ENDPOINTS

**Base URL**: `https://api.qusino.com` (.com) / `https://api.qusino.us` (.us)  
**Auth**: `Bearer <jwt>`  
**WebSocket**: `wss://live.qusino.com` (.com) / `wss://live.qusino.us` (.us)

### Auth & Onboarding

| Method | Endpoint | Description (.com / .us) |
|--------|----------|--------------------------|
| `POST` | `/auth/login/key` | Public key login / Email signup |
| `POST` | `/auth/username` | Set @username (shared) |
| `POST` | `/bonus/first/claim` | 1,000 STAR + 100 Q / 500K STAR + 50 Qs |
| `POST` | `/bonus/daily/claim` | Daily reward |
| `POST` | `/auth/kyc/submit` | Upload docs (fiat/crypto) / ID for redemptions |
| `GET` | `/auth/kyc/status` | Check |

### User & Social (Shared)

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/user/me` | Profile (model-flagged) |
| `GET` | `/buddies` | List |
| `POST` | `/gifts/send` | Send STAR/NFT / STAR/Qs |
| `GET/POST` | `/chat/global` | Global chat |
| `GET/POST` | `/chat/dm/{id}` | DM |

### Poker & Games

| Method | Endpoint | Description (.com / .us) |
|--------|----------|--------------------------|
| `GET` | `/poker/rooms` | List (stakes / fun rooms) |
| `POST` | `/poker/rooms/{id}/join` | Join (Q bets / STAR/Qs) |
| `POST` | `/games/submit` | Submit NFT game |
| `POST` | `/games/{id}/vote` | Vote (1,000 STAR / 50 Qs) |
| `GET` | `/games/floor` | Live games |

### Marketplace (Phase 3)

| Method | Endpoint | Description (.com / .us) |
|--------|----------|--------------------------|
| `GET` | `/marketplace/games` | Listings (real NFTs / virtual) |
| `POST` | `/marketplace/buy/{id}` | Buy NFT |
| `POST` | `/marketplace/purchase/star` | Buy STAR / STAR pack (+ bonus Qs) |

### Cashier

| Method | Endpoint | Description (.com / .us) |
|--------|----------|--------------------------|
| `GET` | `/wallet/balances` | STAR, Q, NFTs / STAR, Qs |
| `POST` | `/wallet/deposit/qubic` | Convert / N/A (free only) |
| `POST` | `/wallet/withdraw/qubic` | Q → QUBIC (KYC) / Qs → Cash (verify) |
| `POST` | `/wallet/mailin/qs` | N/A / Request free Qs |

---

## ADMIN PORTAL – FULLY INTEGRATED

**Base URL**: `https://admin.qusino.com` (.com) / `https://admin.qusino.us` (.us)  
**Login Portal**: `https://admin.qusino.com` (global oversight; role-based access) (hardened)  
**Auth**: `Email + Password + 2FA (TOTP + FIDO2) + IP Whitelist`  
**Session**: 15 min expiry, full audit log

### Logs (Dual)

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/logs/activity` | User actions (model-filtered) |
| `GET` | `/logs/errors` | System/game errors |
| `GET` | `/logs/security` | Login/KYC |
| `GET` | `/logs/export` | CSV export |

### User Management (Dual)

| Method | Endpoint | Description (.com / .us) |
|--------|----------|--------------------------|
| `GET` | `/users` | Search (geo-tagged) |
| `GET` | `/users/{id}` | Full profile |
| `POST` | `/users/{id}/ban` | Ban (cross-model) |
| `POST` | `/users/{id}/kyc/approve` | Approve KYC/redemptions |
| `POST` | `/users/{id}/impersonate` | Login as |

### Game Management

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/games/voting` | Active votes (dual) |
| `POST` | `/games/{id}/launch` | Force launch |
| `POST` | `/games/{id}/retire/flag` | Flag for vote |
| `POST` | `/games/{id}/crush` | Burn NFT/virtual |

### Admin Management

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/admins` | List |
| `POST` | `/admins/create` | Invite |
| `POST` | `/admins/{id}/role` | Assign role (.com/.us/global) |
| `POST` | `/admins/{id}/remove` | Revoke |

### Token Management (Dual)

| Method | Endpoint | Description (.com / .us) |
|--------|----------|--------------------------|
| `POST` | `/tokens/mint/star` | Mint STAR / N/A |
| `POST` | `/tokens/bonus/distribute` | Manual drop (STAR/Q / STAR/Qs) |
| `POST` | `/dividends/trigger` | **Force weekly payout** (Q / Qs prizes) |

### System Settings

| Method | Endpoint | Description |
|--------|----------|-------------|
| `PATCH` | `/settings/kyc-threshold` | Set limit (dual) |
| `PATCH` | `/settings/bonus/daily` | Update reward |
| `POST` | `/settings/announcement` | Push blog (geo-targeted) |
| `POST` | `/settings/maintenance` | Toggle site (per model) |
| `POST` | `/settings/geo-rules` | Update routing/exclusions |

---

## WebSocket Events

| Event | Description |
|-------|-------------|
| `room:state` | Live game (model-specific) |
| `bonus:claimed` | Reward push |
| `vote:update` | Live tally |
| `admin:alert` | KYC flag, bug report, compliance breach |

---

## Smart Contract Hooks (.com Only; .us: Equivalent Off-Chain)

| Action | On-Chain/Process |
|--------|------------------|
| **RNG** | `RANDOM.get()` / Certified PRNG |
| **Vote** | Burn `1,000 STAR` / Enter `50 Qs` |
| **Bonus** | `BONUS.mint()` / Virtual credit |
| **Dividend** | **Weekly Q to NFT holders** / Qs prizes |
| **Crusher** | `NFT.burn()` / Virtual archive |

---

## Tokenomics & Revenue Redistribution (On-Chain Flywheel)

> **Every fee, rake, and yield is 100% distributed via smart contracts (.com) or compliant ledgers (.us) — no black boxes.**

| Recipient | Share | Purpose & Impact (.com / .us) |
|----------|-------|-------------------------------|
| **Qusino Treasury Fund** | **25%** | Core ops: salaries, audits, infra, R&D, compliance (Isle of Man fees / US legal) |
| **CCF (Community Contribution Fund)** | **15%** | Grants, bounties, integrations, governance |
| **In-dApp Liquidity Pool** | **25%** | Auto-injected into QUSINO/QUBIC pair / Virtual liquidity for **Qs** redemptions |
| **Shareholders (QUSINO Stakers & NFT Holders)** | **35%** | **Weekly dividends in Q**, buyback-and-burn, staking rewards / Prize pools from STAR sales |

```mermaid
pie title Qusino Dual Revenue Flywheel
    "Treasury (25%)" : 25
    "CCF – Community (15%)" : 15
    "Liquidity Pool (25%)" : 25
    "Stakers & Holders (35%)" : 35
```

**Deflationary by Design**:  
- **STAR/Qs** burned on votes/entries  
- $QUBIC burned as energy (.com)  
- NFT games crushed → supply reduction  
- Halving emissions every 52 epochs (QUBIC-native; .us: virtual scarcity via burns)

**US Revenue**: From **STAR** packs (entertainment only) + ads; 100% compliant w/ sweepstakes laws.

---

# The Future of Gaming Is Here

**Qusino isn’t a casino — it’s a movement.**

---

**Awaiting further instructions.**

# Flow Charts

# Qusino README Sections: High-Level Flow Charts & Table

Below are **high-level flow charts** for each section of the Qusino README using **Mermaid**, with the **Phased Roadmap** section replaced by a **clean, structured table** for better readability and clarity.

---

## Dual Model Overview

```mermaid
graph TD
    A[User Accesses Platform] --> B{User Location?}
    B -->|Non-US| C[Qusino.com: Real-Money Model<br/>- License: Isle of Man<br/>- Currencies: STAR & Q<br/>- Features: Real Stakes, On-Chain RNG]
    B -->|US| D[Qusino.us: Sweepstakes Model<br/>- Laws: US Sweepstakes<br/>- Currencies: STAR & Qs<br/>- Features: Free Play, Cash Redemptions]
    C --> E[Compliance: KYC/AML, Age Gates, On-Chain RNG]
    D --> E
    E --> F[Shared Social Layer: Buddies, Chat, Wall]
```

---

## Phased Roadmap

| Phase | Timeline | Shared Features | Real-Money (.com) | Sweepstakes (.us) |
|-------|----------|------------------|--------------------|-------------------|
| **Phase 1: Core Build** | Q4 2025 – Q1 2026 | Frontend (HTMX), API, Poker Games, Social Layer, Marketing Site, IPO Prep | Real-stakes tables<br/>QUBIC deposits & withdrawals<br/>KYC/AML | Free STAR & Qs play<br/>Promotional bonuses<br/>Mail-in entries |
| **Phase 2: Expansion** | Q1 2026 – Q3 2026 | Game Portal, Slots, Voting System, NFT Integration | On-chain voting<br/>NFT dividends & staking | Community voting<br/>NFT prizes & virtual assets |
| **Phase 3: Advanced** | Q4 2026+ | Table Games, Marketplace, Leaderboards, Mobile Apps | Real NFT trading<br/>Q token dividends | Virtual NFT gallery<br/>Prize packs & sweepstakes |

> **Key**: Both models share infrastructure, social features, and voting — but diverge in monetization and blockchain usage.

---

## First-Time User Flow

```mermaid
graph TD
    A[User Entry via IP Geo-Routing] --> B{Location?}
    B -->|Non-US: Qusino.com| C[Public Key or Fiat Login<br/>Set @Username]
    B -->|US: Qusino.us| D[Free Email Signup<br/>Set @Username]
    C --> E[Bonus: 1,000 STAR + 100 Q]
    D --> F[Bonus: 500K STAR + 50 Qs]
    E --> G[Daily Login: 500 STAR + 50 Q<br/>7x Streak Multiplier]
    F --> H[Daily Login: 250K STAR + 25 Qs<br/>7x Streak Multiplier]
    G --> I[Play Poker / Games → Win]
    H --> I
    I --> J{Redemption?}
    J -->|.com| K[Cashier → Withdraw Q → KYC → QUBIC/Fiat]
    J -->|.us| L[Redeem Qs → Verify ID → Cash/Gift Card<br/>Or Mail-In for Free Qs]
    K --> M[Logout]
    L --> M
```

---

## Tech Stack

```mermaid
graph TD
    A[Frontend<br/>HTMX + CSS + Animate.js<br/>Cloudflare Geo-Routing] --> B[Real-Time Layer<br/>Socket.IO: Chat, Game Sync]
    B --> C[Backend<br/>Node.js + Express<br/>Dual APIs: .com vs .us]
    C --> D[Auth<br/>.com: Public Key + 2FA<br/>.us: Email + SMS]
    D --> E[Blockchain<br/>.com: QUBIC Tick Chain<br/>.us: Virtual Ledger]
    E --> F[RNG<br/>.com: On-Chain RANDOM.org<br/>.us: Certified PRNG]
    F --> G[Smart Contracts<br/>.com: C++ WASM<br/>.us: Hooks Only]
    G --> H[Infrastructure<br/>AWS + GCP + CDNs<br/>Global Low Latency]
```

---

## Token & Bonus System

```mermaid
graph TD
    A[Token Model] --> B{Platform?}
    B -->|.com| C[STAR = 1,000 QUBIC<br/>Q = 1:1 Redeemable]
    B -->|.us| D[STAR = Fun Only<br/>Qs = Prize-Eligible - Free]
    C --> E[Get Tokens: Deposit, Buy, Bonuses, Marketplace]
    D --> F[Get Tokens: Buy Packs, Free Bonuses, Mail-In, Social]
    E --> G[Bonuses: First - 1K+100, Daily - 500+50, Social - 200/100, Milestones]
    F --> H[Bonuses: First - 500K+50, Daily - 250K+25, Social - 100K+10, Milestones - 500K+50]
    G --> I[Mint: On-Chain<br/>Redeem: Q → QUBIC - KYC]
    H --> J[Mint: Virtual Ledger<br/>Redeem: Qs → Cash - Min 100, ID Verify]
```

---

## Social Wall

```mermaid
graph TD
    A[User Wins or Hits Milestone] --> B[Auto-Broadcast Message]
    B --> C{Privacy Setting?}
    C -->|Public| D[Post to Social Wall<br/>Buddies First → Big Wins → Blog]
    C -->|Private| E[Skip Broadcast<br/>Private Profile]
    D --> F[Cross-Model Sync<br/>.us sees .com teasers<br/>.com sees .us highlights]
    F --> G[Global Feed with Geo Flags]
```

---

## Profile | Chat | Dashboard | Cashier

```mermaid
graph TD
    A[Main Navigation] --> B[Profile<br/>Stats, History, Buddies<br/>.us: Virtual Wins Only]
    A --> C[Chat<br/>Global, DM, Group, In-Game<br/>Shared Across Models]
    A --> D[Dashboard<br/>Sidebar, Notifications<br/>.com: Gifts - .us: Bonuses]
    A --> E[Cashier<br/>View Balances]
    E --> F{Model?}
    F -->|.com| G[QUBIC ↔ STAR<br/>Q → QUBIC/Fiat - KYC<br/>NFTs]
    F -->|.us| H[STAR/Qs Balance<br/>Qs → Cash/Gift - Verify<br/>Mail-In Portal]
    B --> I[Interconnected: Chat from Profile, Notifications to Dashboard]
```

---
# High-Level Poker Room Flow Chart  
**2–9 Players • In-Game Chat • Live Support**

```mermaid
graph TD
    A[User Enters Lobby] --> B{Select Room Type}
    
    B --> C[Cash Games]
    B --> D[Tournaments]
    B --> E[Private Tables]
    B --> F[Practice Mode]
    
    C --> G[Filter by Stakes<br/>Players: 2–9<br/>Speed]
    D --> H[View Schedule<br/>and Formats]
    E --> I[Create or Join<br/>via Invite Code]
    F --> J[Practice Mode<br/>STAR Only]
    
    G --> K[Choose Table<br/>2–9 Players]
    H --> K
    I --> K
    J --> K
    
    K --> L{Seat Available?}
    L -->|Yes| M[Join Table<br/>2–9 Players Max]
    L -->|No| N[Waitlist<br/>or Auto-Seat]
    
    M --> O[Buy-In Screen]
    O --> P{Model?}
    P -->|Real-Money| Q[Pay with Q<br/>or STAR]
    P -->|Sweepstakes| R[Use STAR<br/>or Qs]
    
    Q --> S[Confirm Buy-In]
    R --> S
    S --> T[Enter Game<br/>Table: 2–9 Players<br/>Chat + Support Enabled]
    
    T --> U[Gameplay Loop]
    U --> V[Hand Dealt<br/>to 2–9 Players]
    V --> W[Betting Round<br/>15–30s Clock]
    W --> X{All Actions Complete?}
    X -->|Yes| Y[Next Street<br/>or Showdown]
    X -->|No| W
    Y --> Z[Pot Awarded]
    Z --> AA[Update Balances<br/>Real-Time]
    AA --> BB[New Hand<br/>Auto-Deal]
    
    %% Chat Integration
    T --> CHAT[In-Game Chat Panel<br/>Table Chat + Dealer Messages]
    CHAT --> SEND[Send Message<br/>Emoji, @Mention, Mute]
    SEND --> MOD[Auto-Moderation<br/>Profanity Filter, Spam Block]
    MOD --> DISPLAY[Messages Appear<br/>Pinned Dealer Notes]
    
    %% Support Integration
    T --> SUPPORT[Support Button<br/>Live Help or FAQ]
    SUPPORT --> TICKET[Open Support Ticket<br/>Attach Screenshot]
    TICKET --> RESPONSE[Agent Response<br/>In-Game Chat or Email]
    RESPONSE --> RESOLVED[Issue Resolved<br/>Resume Play]
    
    %% Player Actions
    BB --> CC{Player Action?}
    CC -->|Sit Out| DD[Auto-Fold<br/>Until Return]
    CC -->|Rebuy| EE[Add Chips<br/>Min/Max Limits]
    CC -->|Leave Table| FF[Cash Out<br/>or Forfeit]
    CC -->|Chat| CHAT
    CC -->|Help| SUPPORT
    
    DD --> BB
    EE --> BB
    FF --> GG[Return to Lobby]
    
    N --> K
    GG --> B
```

---

## Feature Summary

| Feature | Implementation |
|--------|----------------|
| **Players per Table** | **2–9** (Heads-Up to Full Ring) |
| **In-Game Chat** | Real-time table chat, emoji, @mentions, mute, dealer messages, auto-moderation |
| **Live Support** | In-game **Support Button** → ticket with screenshot → agent response via chat/email |
| **Game Flow** | Auto-dealt hands, 15–30s action clock, rebuy, sit-out, leave |
| **Cross-Model** | Same flow for Real-Money and Sweepstakes — differs only in currency |
| **No Parentheses or Pipes** | All labels clean and Mermaid-safe |
| **Dark Text on Light Backgrounds** | Ensured via standard Mermaid rendering |

---

## Game Launch Portal (Phase 2)

```mermaid
graph TD
    A[Phase 2: Game Submission] --> B[Submit Game Idea<br/>Cost: 1K STAR or 50 Qs]
    B --> C[Voting Cycle: Mon–Sun UTC<br/>Threshold: 50.01%<br/>Revotable]
    C --> D{Pass?}
    D -->|Yes| E[Add to Live Floor]
    D -->|No| F[Archive Game]
    E --> G[Monitor: Sales, Bugs, 18+ Months]
    G --> H{Retire?}
    H -->|Yes| I[Vote → 1-Week Grace → Archive/Burn]
    H -->|No| E
    I --> J[Dual Governance: Shared Votes<br/>US-Weighted for Social Games]
```

---

## FULL API ENDPOINTS

```mermaid
graph TD
    A[API Base<br/>api.qusino.com - api.qusino.us<br/>Auth: Bearer JWT] --> B[Auth<br/>POST /auth/login<br/>POST /bonus/claim]
    A --> C[User & Social<br/>GET /user/me<br/>POST /buddies<br/>POST /chat/send]
    A --> D[Games<br/>GET /poker/rooms<br/>POST /join<br/>POST /games/vote]
    A --> E[Marketplace Phase 3<br/>GET /marketplace<br/>POST /buy]
    A --> F[Cashier<br/>GET /wallet<br/>POST /deposit<br/>POST /mailin]
    A --> G[WebSocket: wss://live.qusino<br/>Real-Time Game & Chat]
```

---

## ADMIN PORTAL – FULLY INTEGRATED

```mermaid
graph TD
    A[Admin Login<br/>admin.qusino.com<br/>Email + 2FA + IP Whitelist] --> B[Role-Based Access<br/>15min Session, Audit Log]
    B --> C[Logs<br/>GET /logs/activity<br/>GET /logs/security<br/>Export CSV]
    B --> D[User Management<br/>GET /users<br/>POST /ban<br/>POST /kyc/approve]
    C --> E[Full Audit Trail<br/>Model-Filtered, Real-Time]
    D --> E
    E --> F[Hardened Security<br/>Dual Model Segregation]
```

---

