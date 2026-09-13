# Qusino Dice POC — Game Spec

**Status:** POC  
**Game:** Single-die Over/Under  
**Platform:** Qusino (Qubic)  
**RNG:** Qusino protocol RNG (`getRandom`). Qusino calls Qubic `RANDOM`; this capsule does not.  


This document is the source of truth for v1. If it is not in here, it is out of scope.

---

## 1. Goal

Ship one playable, house-banked, provably fair dice game as a Qusino capsule.

Prove, end to end:

1. Fair d6 derived from Qusino `getRandom` (Qusino owns all `RANDOM` SC calls)
2. Over/Under settlement
3. 2% house edge in the paytable (not in the roll)
4. Per-game QSC pot + bet limits so one hit cannot empty the capsule
5. Clean accounting that can later feed Qusino rake / proposer cut

Do **not** build a casino platform in this POC.

---

## 2. Non-goals (explicit)

Leave out of v1:

- Exact-number side bet
- Second die / craps / combo bets
- Global pot shared with other games
- Per-wallet or per-epoch grind caps
- Capsule-level `BuyEntropy` / direct `RANDOM` calls
- Player-chosen unconstrained salt for the roll
- Per-game entropy reserve (Qusino pays `RANDOM`, not this pot)
- Pending multi-tick commit-reveal
- Dynamic edge, VIP odds, boosts
- Using the whole Qusino contract balance as bankroll

Add those after this loop works: bet → roll → pay → pot updates → limits update.

---

## 3. Player-facing rules

- One six-sided die. Faces `1–6`, uniform.
- Player picks:
  - **Side:** `Under` or `Over`
  - **Line:** `2`, `3`, `4`, or `5`
  - **Stake:** integer amount in the play asset (STAR / QSC / Qubic — see §8)
- Win conditions:
  - `Under X` wins if `roll < X`
  - `Over X` wins if `roll > X`
  - Landing **on** the line loses
- One roll per bet. No side tickets on the same roll in v1.

### 3.1 Legal lines

| Bet        | Winning faces | Face count | Chance   |
|------------|---------------|------------|----------|
| Under 4    | 1, 2, 3       | 3          | 50%      |
| Over 3     | 4, 5, 6       | 3          | 50%      |
| Under 3    | 1, 2          | 2          | 33.3%    |
| Over 4     | 5, 6          | 2          | 33.3%    |
| Under 2    | 1             | 1          | 16.7%    |
| Over 5     | 6             | 1          | 16.7%    |

Reject:

- Line not in `{2,3,4,5}`
- Under 1 / Over 6
- Stake below min or above line max
- Payout that would exceed available pot

---

## 4. House edge

The die is fair. Edge lives only in the multiplier.

- Target house edge: **2%**
- RTP: **98%**
- Same edge on every line

Fair multiplier = `6 / faces`  
House multiplier = fair × `0.98`

| Faces | Fair | POC payout |
|-------|------|------------|
| 3     | 2.00× | **1.96×** |
| 2     | 3.00× | **2.94×** |
| 1     | 6.00× | **5.88×** |

### 4.1 Integer payout (no floats)

Always floor. Never round up.

```
payout = floor(bet * 6 * 98 / (faces * 100))
```

`payout` is **total return** (stake + profit). Loser gets `0`.

Examples:

- 3 faces: `bet * 588 / 300` → `bet * 196 / 100`
- 2 faces: `bet * 588 / 200`
- 1 face:  `bet * 588 / 100`

Check overflow before multiply. Use wide intermediates (`uint128` equivalent / QPI `smul` + `div`).

### 4.2 Face helper

```
faces(side, line):
  if side == Under: return line - 1
  if side == Over:  return 6 - line
```

Valid `faces` for v1 is only `{1,2,3}`.

---

## 5. Bankroll model — per-game pot

**Pot is not the Qusino contract balance.**

Qusino holds platform assets (user STAR/QSC, QST inventory, epoch revenue, dividend legs). A dice winner must not be paid from that mixed pile.

Each game (this capsule) has its own bankroll:

```
gamePot         // funds this game may pay winners from
pendingPayouts  // 0 in instant-settle POC; reserved if roll is async
available       = gamePot - pendingPayouts
```

### 5.1 Who funds it

Proposer (or an admin seed function) deposits into `gamePot` before the table opens.

Do **not** open with `gamePot == 0` and hope the first loser funds the first winner.

### 5.2 How the pot moves

On a resolved bet:

1. Take `bet` from the player into `gamePot` (or already escrowed at place-bet).
2. Roll.
3. If lose: `gamePot` keeps the bet. Player gets 0.
4. If win: pay `payout` from `gamePot` to the player.
   - Net pot change on a win: `+bet - payout` (negative; the 2% makes this smaller than a fair 2×/3×/6× hole).

Rake skim to Qusino epoch revenue / 33.3% proposer cut is **after** the game is stable. For POC it is enough to leave edge sitting in `gamePot` and log gross handle + net pot change. Do not implement the full Qusino split inside the dice loop unless the platform already requires it on every wager.

### 5.3 Table closed

If `max_win` (below) < minimum payable win for a line, reject that line.  
If every line is unpayable, the table is closed until the pot is topped up.

---

## 6. Bet limits

Three limits. Do not ship a single global max bet.

### 6.1 Min bet (fixed)

One constant for all lines.

Purpose: stop dust / spam against `RANDOM` + state.

Name it `MIN_BET`. Exact value is an implementation parameter (asset units). Do not scale min by faces.

### 6.2 Max win = 3% of available pot

```
max_win = floor(available * 3 / 100)
```

This is the largest **total payout** one roll may take from the pot.

3% is the POC knob. Not extra house edge. Change later with volume.

### 6.3 Max bet derived per line

So that `payout(max_bet) <= max_win`:

```
max_bet(faces) = floor(max_win * faces * 100 / (6 * 98))
```

Same pot, same max hole, smaller stake on the 5.88× line.

Worked example, `available = 1_000_000`, `max_win = 30_000`:

| Line              | Faces | Multiplier | max_bet |
|-------------------|-------|------------|---------|
| Under 4 / Over 3  | 3     | 1.96×      | 15,306  |
| Under 3 / Over 4  | 2     | 2.94×      | 10,204  |
| Under 2 / Over 5  | 1     | 5.88×      | 5,102   |

Reject if `bet > max_bet(faces)` or if computed `payout > available` or `payout > max_win`.

If `max_bet < MIN_BET` for a line, that line is closed.

### 6.4 Print on the slip

```
Under 2 · faces 1 · 5.88× · edge 2%
min 1000 · max bet 5102 · max win 30000
pot available 1000000
```

Players should see why the long shot is capped.

---

## 7. Qusino RNG interface

Source: Qusino `RNG` upgrade outline (protocol-level RANDOM).

Qusino centralizes every `RANDOM` SC call. It keeps a **per-game pool** of pre-fetched values plus a shared **overflow reserve**, restocked with bulk `BuyEntropy`. Capsules request one value:

```
raw = Qusino.getRandom(gameId, salt)
```

This capsule **must not** call `RANDOM` / `BuyEntropy` itself. If `getRandom` fails (empty bank and no fallback), reject the bet (`RANDOM_UNAVAILABLE`). Do not invent a local PRNG.

### 7.1 Salt (required)

Do **not** accept a free-form player salt.

Qusino pool slots live in public contract state. If the player can choose `salt` in the same transaction as the bet, they can grind salts offline until the pool index maps to a known winning word.

Bind salt in the capsule:

```
salt = hash(gameId || player || betId || side || line || bet || tick)
```

Pass that into `getRandom`. No extra user field.

### 7.2 Slot value is a seed, not the face

Do **not** use the returned pool word as the die face, and do not do `raw % 6` on a public slot.

Knowing the slot would then mean knowing the roll. Mix again after the pull:

```
mixed = hash(raw || salt || gameId || player || betId)
roll  = uniform_d6(mixed)
```

So: Qusino picks/consumes one banked word; this game derives the d6 from that word plus bet-bound data.

### 7.3 Mapping to a d6

Must be uniform. Do not use `mixed % 6` if the source range is not a multiple of 6 (modulo bias).

POC pattern — rejection sampling on a wide integer taken from `mixed`:

```
take N bits from mixed (N >= 32)
if value >= floor(2^N / 6) * 6: derive more bits / next hash block and retry
else roll = (value % 6) + 1
```

If a retry is impossible in-contract, consume a second `getRandom` rather than accept bias. Document the exact mapping in code comments and the game URI.

### 7.4 STAR vs QSC

Same `getRandom` path. Same `uniform_d6`. Qusino pays `RANDOM`; this pot does not.

- QSC: real bankroll, real solvency.
- STAR: same faces, plus a **per-identity rate limit** so free play cannot empty a game pool and force constant reserve refreshes (those still burn Qubic at Qusino).

Do not build a per-game `entropyReserve` or siphon QSC edge to pay `bitFee`. That is Qusino’s protocol cost.

### 7.5 High-stakes bypass

Qusino’s outline allows an optional direct `RANDOM` call for high stakes. **Out of scope for POC.** Every v1 roll goes through `getRandom`.

### 7.6 Logs for replay

Every pull must be reconstructable:

```
gameId, betId, player, tick, rawRef (id/index of consumed slot), mixed, roll
```

Players should be able to replay: bank fill → consumed slot → bound salt → mixed → face.

---

## 8. Settlement flow (POC)

One procedure: place + resolve in the same invocation. `getRandom` is specified as atomic (one tx, one value).

```
validate invocator, side, line, bet
faces = faces(side, line)
require faces in {1,2,3}
require bet >= MIN_BET
require available pot sufficient
max_win = floor(available * 3 / 100)
max_bet = floor(max_win * faces * 100 / (6 * 98))
require bet <= max_bet
payout = floor(bet * 6 * 98 / (faces * 100))
require payout <= available
require payout <= max_win

take bet from player into gamePot          // QSC for v1

salt = hash(gameId || player || betId || side || line || bet || tick)
raw  = Qusino.getRandom(gameId, salt)      // fail closed if unavailable
roll = uniform_d6(hash(raw || salt || gameId || player || betId))
won  = (side == Under && roll < line) || (side == Over && roll > line)

if won: pay payout from gamePot to player
else:   pay 0

emit log: gameId, player, side, line, bet, roll, won, payout, potAfter, rawRef
```

If Qusino later makes `getRandom` async, then split into `placeBet` (reserve `payout` in `pendingPayouts`) and `resolveBet`. POC assumes sync.

---

## 9. Assets / Qusino modes

Qusino play modes: STAR (free play), QSC (sweepstakes), Qubic.

**POC pot asset is QSC.** Winners are paid QSC. 1 QSC = 100 Qubic is Qusino’s redemption peg, not this capsule. Do not hold matching Qubic inside `gamePot`.

STAR may share the same rules/math with a separate STAR pot if free-play is required for launch. Do not mix STAR and QSC in one pot.

Rules that do not change by asset:

- Per-game pot in **that same asset**
- Same paytable
- Same 3% max-win
- Same `getRandom` derivation

Rules that do change:

- STAR pot is soft (STAR is not redeemable for Qubic).
- QSC pot is real solvency. Never pay more than `available`.
- Qubic backing for redeemable QSC lives in Qusino’s redemption reserve (`circulating redeemable QSC × 100`), including QSC sitting in this `gamePot`.

---

## 10. Suggested state (minimum)

Per game / capsule:

```
gameId
proposer
gamePot             // QSC
pendingPayouts      // 0 if sync resolve
minBet              // or a compile-time constant
maxWinBps           // 300 = 3.00%
edgeBps             // 200 = 2.00%  (paytable uses 98)
```

Per resolved bet (log or history slot if you need replay):

```
betId
player
side        // 0 = Under, 1 = Over
line        // 2..5
bet
faces
roll        // 1..6
won
payout
potBefore
potAfter
rawRef      // consumed Qusino pool slot / value id
tick
salt        // bound hash, not player-supplied
```

Keep history bounded. POC does not need a full ledger of every spin if `LOG_INFO` is enough to reconstruct.

---

## 11. Procedures / functions the dev should expose

**Writes**

- `seedPot` — proposer (or allowed seeder) adds funds to `gamePot`
- `play` — validate, take QSC bet, `getRandom`, derive d6, settle
- optional `withdrawPot` — proposer pull-down, only `available`, never while it would break reserved payouts

**Reads**

- `getTable` — pot, available, minBet, maxWin, maxBet per line
- `quote(side, line, bet)` — faces, multiplier payload, payout, accept/reject reason
- `getBet(betId)` if async or if history is stored

`quote` should use the same integer math as `play`. No second implementation.

---

## 12. Failure codes (suggested)

Use explicit codes, refund invocation reward on user error if that is Qusino/Qubic convention.

- `INVALID_LINE`
- `INVALID_SIDE`
- `BET_TOO_SMALL`
- `BET_TOO_LARGE`
- `LINE_CLOSED`          // max_bet < min or max_win unusable
- `INSUFFICIENT_POT`
- `INSUFFICIENT_PLAYER_FUNDS`
- `TABLE_CLOSED`
- `RANDOM_UNAVAILABLE`
- `NOT_AUTHORIZED`       // seed/withdraw

---

## 13. What “done” means for this POC

The POC is done when all of the following are true:

1. Every legal line settles with the floor formula above.
2. Landing on the line always loses.
3. Capsule never calls `RANDOM` / `BuyEntropy`; only `Qusino.getRandom`.
4. Salt is contract-bound; no player-chosen salt.
5. Face is `uniform_d6(hash(raw || bound fields))`, not `poolWord % 6`.
6. Mapping is uniform and documented.
7. A winning 1-face bet cannot pay more than 3% of available QSC pot.
8. After a win, `max_bet` on every line drops with the pot.
9. After a loss, pot and limits rise.
10. Empty / too-small pot refuses bets instead of underpaying.
11. Qusino platform balance is never used as `gamePot`.
12. A quote function matches live settlement on the same inputs.
13. Logs are enough to reconstruct any single round (slot consumed → salt → mixed → face).

---

## 14. Parameters to fill in before coding

These are not design questions; they are deployment values:

| Param            | POC default           | Notes                                         |
|------------------|-----------------------|-----------------------------------------------|
| Die              | 1d6                   | locked                                        |
| Lines            | 2,3,4,5               | locked                                        |
| Edge             | 2%                    | locked for POC                                |
| Max win          | 3% of available       | locked for POC                                |
| Asset            | QSC                   | locked for POC pot                            |
| RNG              | `Qusino.getRandom`    | locked; Qusino owns `RANDOM`                  |
| Min bet          | TBD                   | QSC units                                     |
| Seed pot size    | TBD                   | must cover at least one max 1-face hit        |
| STAR rate limit  | TBD                   | if STAR mode is on                            |

---

## 15. Later (not this ticket)

- Exact-number side bet at the same 5.88× / 2% (1 face)
- Under 6 / Over 1 (5-face grinders at 1.176×)
- Shared LP vault with per-game exposure caps
- Skim 2% edge on a cadence into Qusino epoch revenue + proposer 33.3%
- Per-address QSC velocity limits
- Qusino high-stakes direct-`RANDOM` bypass
- Paid-play surplus funding Qusino’s entropy reserve (protocol, not this pot)

---

## 16. One-screen summary for the implementer

```
Fair d6 via Qusino.getRandom. Capsule never calls RANDOM.
salt = hash(gameId || player || betId || side || line || bet || tick)
roll = uniform_d6(hash(raw || salt || gameId || player || betId))
Under X if roll < X. Over X if roll > X. Tie line loses.
Lines 2–5 only.
payout = floor(bet * 6 * 98 / (faces * 100))
gamePot is QSC. Never Qusino’s global balance.
max_win = floor(available * 3 / 100)
max_bet = floor(max_win * faces * 100 / (6 * 98))
Floor everything. Reject rather than underpay.
No side bets. No player-chosen salt.
```
