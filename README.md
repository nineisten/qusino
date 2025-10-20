# Qusino MVP Architecture Outline

**Project Overview**: Qusino is an online social casino built on the Qubic blockchain, offering a Texas Hold'em poker room with real-time chat, provably fair outcomes via the RANDOM smart contract, and a U.S.-compliant sweepstakes model (similar to Stake.us). Transactions use Qubic (no fiat), with future support for native tokens (CFB, CODED). The MVP focuses on a lean poker room, chat, and admin tools, with Gold Tokens (non-redeemable) for fun play and in-app purchases, and Sweep Tokens (redeemable) for prize-eligible gameplay.

**Key Specifications**:
- **Exchange Rate**: 1,000 Qubic = 1 Gold Token + 1 Sweep Token (50/50 ratio).
- **Tokenomics**: 50% to smart contract (SC) holders, 25% to Dev/Corp, 10% to Community Contribution Fund (CCF), 15% burn.
- **Timeline**: 6 weeks for MVP delivery (target: December 1, 2025).
- **Frontend**: HTMX with standard CSS for server-driven, responsive UI.
- **Legal**: Sweepstakes model with no-purchase-necessary entry, age (21+) and geolocation restrictions.

## 1. System Overview
**System**: 
- **MVP Features**:
  - User registration/login via Qubic wallet.
  - Poker room: Multi-table Texas Hold'em with real-time gameplay and chat.
  - Token system:
    - **Gold Tokens**: Non-redeemable, for Fun Mode and in-app purchases (avatars, table themes, emojis).
    - **Sweep Tokens**: Redeemable for Qubic, obtained via free methods (sign-up, daily bonuses, mail-ins) or as bonuses with Gold purchases.
  - Provably fair outcomes using Qubic’s RANDOM SC (index 3).
  - Admin tools: Monitoring, chat moderation, user management, redemption approvals.
- **Legal Compliance**: No-purchase-needed (free Sweep Tokens), KYC for large redemptions, geoblock restricted states (e.g., WA, ID).
- **Future Scope**: Table games, slots, additional tokens, Quottery integration.

## 2. Tokenomics
- **Revenue Allocation** (per Qubic deposited):
  - **50% SC Holders**: Distributed to users holding/staking Qubic in RANDOM SC or Qusino-specific SC.
  - **25% Dev/Corp**: Funds development, hosting, compliance, marketing.
  - **10% CCF**: Supports user rewards, promotions, or charity.
  - **15% Burn**: Sent to null address or burned via Qubic SC to reduce supply.
- **Implementation**:
  - Backend splits deposits on-chain: 50% to SC, 25% to Dev/Corp wallet, 10% to CCF wallet, 15% burned.
  - Ensure transparency with auditable transactions via Qubic explorer.
  - Future: Dynamic governance SC for allocation adjustments.

## 3. Token System and In-app Purchases
- **Gold Tokens (Non-redeemable)**:
  - **Purpose**: Fun Mode poker and in-app purchases.
  - **Acquisition**:
    - Free: Sign-up (10,000 Gold Tokens), daily logins, promotions.
    - Purchase: 1,000 Qubic = 1 Gold Token.
  - **In-app Purchases** (priced for high-value Gold Tokens):
    - Avatar: 0.5 Gold Tokens (500 Qubic equivalent).
    - Premium table theme: 1 Gold Token (1,000 Qubic equivalent).
    - Emojis/badges: 0.2 Gold Tokens (200 Qubic equivalent).
  - **Advantage**: No cash-out liability, encourages platform engagement.
- **Sweep Tokens (Redeemable)**:
  - **Purpose**: Sweepstakes Mode poker, winnings redeemable for Qubic.
  - **Acquisition**:
    - Free: Sign-up (25 Sweep Tokens), daily bonuses (5-10/day), mail-ins (10-50/postcard), challenges.
    - Bonus: 1,000 Qubic = 1 Gold + 1 Sweep Token (50/50 ratio).
  - **Redemption**: 1x playthrough, KYC for redemptions > 1,000 Sweep Tokens, geolocation check. 1 Sweep Token = 1 Qubic.
- **Implementation**:
  - Track separate Gold/Sweep balances in database.
  - UI clearly separates Fun Mode (Gold) and Sweepstakes Mode (Sweep).
  - In-app store for Gold Token purchases.

## 4. Frontend
- **Tech Stack**: HTMX + standard CSS for styling, WebSockets (Socket.io) for real-time updates, minimal JavaScript for specific features (e.g., canvas card rendering).
- **Why HTMX**: Server-driven UI via HTML partials, reducing client-side complexity, ideal for rapid MVP development.
- **Styling**: Use standard CSS for responsive design, ensuring mobile and browser compatibility. Define custom styles for dashboard, poker tables, chat, and store, with a focus on clean, minimal design.
- **Components**:
  - **Dashboard**: Show Gold/Sweep balances (`hx-get="/dashboard"`), buttons for deposit (`hx-post="/tokens/deposit"`), redemption (`hx-post="/tokens/redeem"`), store (`hx-get="/store"`).
  - **In-app Store**: Item grid (`hx-get="/store/items"`), purchase modal (`hx-post="/store/purchase"`).
  - **Poker UI**: Toggle Fun/Sweep modes (`hx-get="/games/mode"`), real-time updates via WebSockets, fallback polling (`hx-poll`). Canvas/WebGL for cards.
  - **Chat UI**: Real-time messages via Socket.io, initial load/refresh via `hx-get="/chat/messages"`.
  - **Redemption Flow**: Form (`hx-post="/tokens/redeem"`) with dynamic KYC prompts.
- **Responsive Design**: Use CSS media queries for mobile/browser compatibility, test for future PWA support.

## 5. Backend
- **Tech Stack**: Node.js + Express.js (TypeScript), Docker for scalability.
- **Endpoints** (HTMX-compatible):
  - `/tokens/deposit` (POST): Verify Qubic deposit, credit 1 Gold + 1 Sweep per 1,000 Qubic, allocate per tokenomics, return dashboard HTML.
  - `/tokens/redeem` (POST): Validate Sweep balance/playthrough/KYC, initiate Qubic payout, return confirmation HTML.
  - `/store/purchase` (POST): Deduct Gold Tokens, update inventory, return store HTML.
  - `/tokens/free-sweep` (POST): Process free Sweep Token requests, return balance HTML.
  - `/dashboard` (GET): Render Gold/Sweep balance HTML.
  - `/store/items` (GET): Render store item grid HTML.
  - `/games/mode` (GET): Render Fun/Sweep mode toggle HTML.
  - `/chat/messages` (GET): Render chat message HTML.
- **Game Logic**: Server-authoritative poker, separate Gold/Sweep games, RANDOM SC for fair deals.
- **Session Management**: Redis for caching, JWT for auth, include session data in HTML responses.

## 6. Blockchain Integration
- **Qubic Integration**:
  - **Deposits**: User sends Qubic to platform address, verify via qubic-cli/SDK, credit 1 Gold + 1 Sweep per 1,000 Qubic, distribute per tokenomics.
  - **Payouts**: Send Qubic to user’s wallet post-redemption, use multi-sig for security.
  - **RANDOM SC**: Call index 3 contract for random seeds, store for verification.
- **Tokenomics**:
  - Per 1,000 Qubic deposited: 500 Qubic to SC holders, 250 Qubic to Dev/Corp, 100 Qubic to CCF, 150 Qubic burned.
  - Use Qubic’s fee-less transactions.
- **Future**: Support CFB, CODED via Qubic SCs.

## 7. Database
- **Tech Stack**: PostgreSQL, Prisma ORM.
- **Schema**:
  - **Users**: `id`, `qubic_address`, `gold_balance`, `sweep_balance`, `inventory` (JSON), `role` (user/admin/moderator).
  - **Transactions**: Log deposits, redemptions, store purchases, tokenomic allocations.
  - **Games**: Track mode (Gold/Sweep), RANDOM seed.
  - **Store**: Items (`id`, `name`, `gold_cost`, `type: cosmetic/gameplay`).
- **Handling**: Implement backups, GDPR-like privacy.

## 8. Admin Tools
- **Tech Stack**: HTMX-based dashboard with standard CSS, role-based access (super admin, moderator).
- **Features**:
  - **Monitoring**: View tables, sessions, token flows, tokenomics via `hx-get="/admin/monitor"`.
  - **Chat Moderation**: Ban users, delete messages (`hx-post="/admin/chat"`), AI flagging (future).
  - **User Management**:
    - View/edit profiles (`hx-get="/admin/users"`).
    - Ban/suspend accounts (`hx-post="/admin/users/ban"`).
    - Assign roles (`hx-post="/admin/users/role"`).
    - Adjust tokens (`hx-post="/admin/users/tokens"`).
    - Audit activity (`hx-get="/admin/users/audit"`).
  - **Redemption Approval**: Review large redemptions, verify KYC/geolocation (`hx-post="/admin/redeem"`).
  - **Store Management**: Add/edit items (`hx-post="/admin/store"`).
  - **Tokenomics Oversight**: Monitor allocations, verify burns (`hx-get="/admin/tokenomics"`).
- **Access**: Audit logs, multi-factor auth.

## 9. Security and Compliance
- **Security**: HTTPS, rate limiting, multi-sig Qubic wallets, OWASP practices.
- **Compliance**:
  - **Sweepstakes**: No-purchase-needed, clear Gold (fun/purchases) vs. Sweep (prizes) in UI/ToS.
  - **Validation**: KYC for redemptions > 1,000 Sweep Tokens, geoblock restricted states (e.g., WA, ID).
  - **Audits**: Public RANDOM SC logs, transparent tokenomic allocations.
- **Scalability**: AWS/GCP, auto-scaling.

## 10. Development Roadmap
- **Timeline**: 6 weeks for MVP (target: December 1, 2025).
- **Phases**:
  - **Weeks 1-2**: Poker room, chat, wallet login, token system, HTMX/CSS setup.
  - **Weeks 3-4**: 1,000 Qubic = 1 Gold + 1 Sweep, in-app store, user management, RANDOM SC, tokenomics.
  - **Weeks 5-6**: Admin tools (user management, tokenomics), compliance, testing (unit, integration, security).
- **Tools**: Git, GitHub Actions for CI/CD.

## 11. Implementation Notes
- **HTMX**: Use `hx-get`, `hx-post`, `hx-swap` for dynamic HTML updates. Minimize client-side JS to canvas animations and WebSocket handling.
- **CSS**: Write custom CSS for clean, minimal design. Use media queries for responsive layouts (mobile, desktop). Prioritize performance with lightweight styles.
- **Token System**: Hardcode 1,000 Qubic = 1 Gold + 1 Sweep, adjustable via config. Ensure clear UI separation of Gold/Sweep modes.
- **Tokenomics**: Implement deposit splitting in backend, verify burns on-chain.
- **Testing**: Focus on deposit/redemption flows, RANDOM SC integration, HTMX response reliability, and CSS responsiveness.
- **Compliance**: Ensure user-friendly no-purchase-needed options (e.g., mail-in Sweep Tokens).

This architecture provides a clear, actionable blueprint for the Qusino MVP. For questions or additional details (e.g., HTMX/CSS snippets, database schema), contact the project lead.