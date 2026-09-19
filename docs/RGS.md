# Qusino Engine Spec (for implementers)

**Status:** Product / tokenomics design. Not live in `Qusino.h` until a quorum logic upgrade.  
**Audience:** Core SC, frontend, SDK.  
**Date:** 2026-09-19

---

## 1. What we are building

Qusino is **not** a consumer casino brand.

It is the **QX of wagering** on Qubic:

- listing + RNG + bankroll + rake split
- anyone can build a casino **on** it
- anyone can submit a game **to** it

| Role | Job | Pays / gets paid |
|---|---|---|
| **Engine (Qusino SC)** | Settle bets, RNG bank, epoch split | Epoch remainder → shares / LP / CCF / treasury |
| **Game dev** | Capsule: rules, RTP, art | Cut of that game’s GGR |
| **Operator** | Skin: app, users, cashier, lobby | Cut of GGR on bets they originated |
| **Player** | Bets QSC or STAR | Belongs to the operator, not to Qusino marketing |

Reference play (Coin Flip on a public URL) exists only to prove the engine is real.

---

## 2. What stays the same

Do not rip these out.

- Qusino smart contract on Qubic; 676 SC shares; `distributeDividends`
- Result Bank + `refillRandomBank` + RANDOM SC (capsules never call RANDOM)
- Coin Flip pattern: QSC or STAR, instant settle, ~2% edge (`QUSINO_COINFLIP_PAYOUT_PERCENT` ≈ 1.96×)
- QSC = 100 Qu; STAR is not redeemable for Qu
- `submitGame` / STAR vote / revote window / failed-game list
- Bankroll must cover QSC wins or the bet rejects
- First-party tables (CF, later BJ / BA) as **reference games**
- Computor quorum required for logic-changing upgrades

---

## 3. What changes

### 3.1 Positioning and surfaces

| Surface | Job |
|---|---|
| **qusino.com** | Engine site: docs, SDK, fee table, GLP, operator/dev signup, stats |
| **play.qusino.com** or **qusino.online** | Reference skin only. Banner: “Demo skin on the Qusino engine.” |
| Marketing | Operators + game devs (devrel). Not player UA. |

Success metrics: live capsules, bonded operators, **QSC handle and GGR**. Not STAR claims or DAU.

### 3.2 Players vs the 131k map

`QUSINO_MAX_USERS` (131,072) is **not** the customer database.

- Operators may have millions of off-chain accounts.
- Engine sees `operatorId` + settlement, not every casual user as a `userAssetVolume` row.
- Self-custodial power users can still hold QSC/STAR in the SC.
- QST is **not** a gate to play. QST is listing / bond / VIP across skins.

### 3.3 Money: order of operations

**GGR** = Qu-backed take (bets − payouts), or declared rake. STAR play does not create Qu GGR.

```
GGR
  ├─ game dev (proposer)     OFF THE TOP
  ├─ operator (operatorId)   OFF THE TOP
  └─ remainder → epochRevenue
        END_EPOCH:
          ├─ SC shareholders
          ├─ LP
          ├─ CCF
          └─ treasury
```

Dev and operator are vendors on the bet. They are not paid from treasury after the fact.

Bets **must** carry `operatorId` (fee recipient). Missing id:

- use official Qusino operator account, **or**
- fold that slice into epoch  

Pick one and hard-code it. Recommendation: official operator account so the reference skin has a P&L.

### 3.4 Target splits (v1 proposal — tune in constants)

**Slots / third-party capsules (QSC GGR):**

| Bucket | % of GGR |
|---|---|
| Game dev | 25% |
| Operator | 25% |
| Epoch | 50% |

**First-party tables (no capsule owner):**

| Bucket | % of GGR |
|---|---|
| Game dev | 0% |
| Operator | 25% |
| Epoch | 75% |

**Epoch remainder:**

| Bucket | % of epoch |
|---|---|
| SC shareholders | 50–55% (replaces old 20% shares + old 30% QST) |
| LP | 15–20% |
| CCF | 5% |
| Treasury | 15–20% (do not zero; this funds SDK/indexer/RNG top-up) |

Old live constants for reference (replace in upgrade):

- `QUSINO_SHAREHOLDERS_DIVIDENDS_PERCENT = 20`
- `QUSINO_QST_HOLDERS_DIVIDENDS_PERCENT = 30` → **0** (utility remains; dividend removed)
- `QUSINO_LP_DIVIDENDS_PERCENT = 20`
- `QUSINO_CCF_DIVIDENDS_PERCENT = 5`
- `QUSINO_TREASURY_DIVIDENDS_PERCENT = 25`
- `QUSINO_DEVELOPER_FEE = 333` (33.3%) → retune to 250 (25%) plus new operator fee 250

Dividend math for 676 shares stays the same shape:

```
perShare = (epochRevenue * SHARE_PERCENT) / 67600
distributeDividends(perShare)
```

### 3.5 Worked example

Slot GGR = 100 Qu on a bonded operator skin.

- Dev 25
- Operator 25
- Epoch 50
  - Shares 50% → 25 to 676 shares (~0.037 Qu/share on this tiny example)
  - LP 20% → 10
  - CCF 5% → 2.5
  - Treasury 25% → 12.5  
  (Use the lean epoch table you ship; numbers above illustrate **order**, not final percents.)

---

## 4. Operators

### 4.1 Do not vote operators in

STAR / GLP voting is for **games only**.

Operators:

- register on-chain (`operatorId`, `feeRecipient`, metadata URI)
- post a **Qu bond**
- sign Qusino entity ToS for featured listing + production indexer keys

Permissionless raw SC calls can still work (QX-like). Featured directory and official SDK production access require a live bond ≥ floor.

### 4.2 Bond (stake + slash)

**Asset:** Qu (not STAR).  
**Floor (v1):** `max(FIXED_FLOOR, k * trailing weekly GGR)` — start with a fixed floor only if GGR oracle is not ready.  
**Unbond:** 2 epochs, **zero open QSC exposure**.  
**v1 slash only (automatic in SC):** unpaid QSC wins (booked win the bankroll did not cover).

Slash destination:

1. short-paid winners
2. remainder → `epochRevenue`

Do not slash for “players lost” or “we dislike the lobby.”

Optional later: paytable-hash mismatch; ToS slash via documented multisig + reason hash.

### 4.3 Bankroll

Do **not** keep one global `bonusAmount` that every operator can drain.

v1 acceptable design:

- `bankroll[operatorId]` (and/or per `gameId`)
- bet rejected if that book cannot cover the max win
- `depositBonus(operatorId)` permissioned to operator (and engine admin for the official skin)

RNG reserve fees: charge the operator whose pool is consumed, or amortize from their bankroll.

---

## 5. Games / GLP

Stay:

- submit fee → epoch
- STAR vote yes/no
- revoke after window / negative path

Add / enforce on submit:

- paytable / RTP commitment hash
- max exposure per bet
- asset types allowed (QSC, STAR, or both)
- proposer `feeRecipient`

Outside-dev slots are the catalog. CF / BJ / BA are reference implementations of the engine.

Capsules request **one** random from Qusino (`getRandom` / bank). Never RANDOM directly.

---

## 6. SDK (required)

Three packages, one engine.

| Package | Who | Functions |
|---|---|---|
| `@qusino/engine` | Both | connect, balances, `placeBet`, errors (`RNG_NOT_READY`, insolvency, min bet), bank status |
| `@qusino/glp` | Devs write, ops read | `submitGame`, metadata, vote status, list live capsules |
| `@qusino/operator` | Operators | register, bond/unbond, deposit bankroll, `operatorId` on bets, refill webhook, net-settle helper |

Rules:

- TypeScript first (web + Node).
- No KYC, cards, or geo in the SDK.
- Reference skin must use this SDK (dogfood).
- Docs live on qusino.com. “Time to first bet” is the north-star UX.

---

## 7. Tokens (do not confuse these)

| Asset | Role after upgrade |
|---|---|
| **676 SC shares** | Engine equity. Get epoch shareholder %. |
| **QST** | Membership / listing / optional bond / VIP. **No epoch dividend.** |
| **QSC** | Qu-backed chips. Creates GGR and dividends. |
| **STAR** | Free play + vote fee burn. Not Qu. |

---

## 8. Legal / product (entity, not SC)

Qusino entity:

- ToS: operator is the casino; Qusino is settlement API
- featured directory, brand use, indexer keys
- does **not** license the operator’s gambling
- `.com` / `.us` geo products are **operator** problems

Do not put that in QPI.

---

## 9. Implementation checklist (SC)

- [ ] `operatorId` on bet / settle procedures
- [ ] `registerOperator` + metadata
- [ ] `bondDeposit` / `bondUnbond` / lock epochs
- [ ] automatic slash: unpaid QSC win
- [ ] per-operator (or per-game) bankroll
- [ ] constants: dev %, operator %, epoch %s; QST dividend % = 0
- [ ] `END_EPOCH`: pay dev+operator already settled per-bet **or** accrue and flush; then split remainder
- [ ] stop assuming every player is a `userAssetVolume` insert
- [ ] paytable hash stored on `GameInfo`
- [ ] events for: bet, settle, slash, bond, refill, epoch split
- [ ] tests: fair split math, insolvency reject, slash rebate, unbond blocked with open exposure, missing operatorId fallback

Frontend / SDK:

- [ ] qusino.com engine site
- [ ] demo host with one public table
- [ ] operator dashboard: bond, bankroll, GGR
- [ ] GLP submit flow with RTP + hash

---

## 10. Ship order

1. Freeze this spec (splits + operatorId + bond v1).
2. SDK stubs + .com docs against current SC (read-only where needed).
3. Move play UI off the marketing domain.
4. Quorum proposal: logic upgrade.
5. Official skin uses `operatorId = Qusino`.
6. Onboard 1–2 external operators with bonds.
7. BJ / BA + third-party slots.

---

## 11. Explicit non-goals (v1)

- Voting operators in
- DraftKings-scale consumer UA
- Fiat, KYC, or state licensing inside the contract
- STAR as operator collateral
- Zero treasury
- Every app user as an on-chain Qusino user row

---

## 12. One-line summary for the PR

Qusino becomes a listing + RNG + bankroll + rake engine. Game studios and casino operators take GGR off the top. The remainder is epoch revenue. QST dividends move to SC shareholders. Operators post a Qu bond. Players belong to skins. The public Coin Flip table is a demo, not the company.
