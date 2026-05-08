# SoberFlare

SoberFlare is a Web3 social application centered on address-based identity. The current codebase has evolved beyond its early scaffold into a runnable combination of a macOS desktop client and a NestJS backend, built around content feeds, relationship networks, end-to-end encrypted messaging, and on-chain wallet capabilities. A mobile version is being developed in parallel.

## Current Capabilities

### macOS Desktop
A standalone React Native macOS entrypoint with custom desktop routing and a multi-column layout. It already covers the home feed, relationship network, contacts, chat, notifications, profile, as well as desktop panes for wallet overview, assets, receive, transfer, swap, keys, version updates, and more.

### Mobile
Built with React Native + Expo, using Expo Router to manage wallet initialization, home tabs, relationships, chat, contacts, notifications, post details, assets, QR scan, transfer, swap, wallet keys, and other page flows.

### Feed and Posts
The client is already connected to a multi-channel home feed, post composition, post detail pages, and comment interactions. The backend provides feed distribution, post read/write APIs, and media upload-related capabilities.

### Backend
The NestJS API is mounted under `/api` and currently includes the following modules: `auth`, `chat`, `feed`, `posts`, `notifications`, `profile`, `life-tree`, `ai`, `relationship-ingestion`, `relationship-network`, `wallet-broadcast`, and `health`.

### Data Layer
PostgreSQL / Supabase serves as the backend’s primary data source. Database changes are managed through `supabase/migrations`. On the client side, SQLite is used for local caching, while secure storage is used for sensitive data such as wallets and chat keys.

### Chat
Supports address-based direct messages, group chats, private-group E2EE, device sync state, and supporting data capabilities such as chat blacklists and hidden conversations.

### Relationship Network
Supports wallet transfer event ingestion, relationship projection refresh, relationship querying, and delayed wallet address synchronization on mobile.

### Wallets and Transfers
The client locally holds private keys and performs signing. The server is responsible for constructing unsigned transactions and broadcasting signed transactions. The current flow supports Ethereum, Bitcoin, Solana, BNB Smart Chain, and Tron.

### Workers
The backend includes two long-polling workers: `chat-e2ee` and `relationship-network`. They handle private-group sync tasks and relationship graph refresh tasks respectively, and report heartbeat, queue depth, and error state through health endpoints.

---

## Mobile and Desktop Clients

### Mobile Entry
The mobile entrypoint is in `mobile/app`. The root layout first checks wallet initialization status:

- If wallet initialization is incomplete, the user enters `wallet-setup`
- Once completed, the user enters the main tab flow and the app delays syncing wallet addresses needed by the relationship network

The current tab navigation directly exposes `Home`, `Network`, `Chat`, and `Me`. The `tree` route is still preserved, but hidden from the tab bar.

In addition to tabs, the mobile root stack also mounts routes for `compose`, `contacts`, `contact/[address]`, `direct-chat`, `group-chat`, `notifications`, `post/[id]`, `assets`, `scan`, `transfer`, `swap`, `transfer-history`, and `wallet-secrets`.

### macOS Entry
The macOS entrypoint is `mobile/index.macos.js`, and the desktop UI lives in `mobile/src/desktop`, with the following core layers:

- `App.tsx`: desktop shell, side navigation, settings menu, account switcher menu, and left/right pane layout control
- `router.tsx`: in-memory desktop routing and backward / forward history
- `routes.tsx`: main route definitions for the left pane, including Home, Network, Contacts, Chat, Me, Notifications, and Settings
- `detailRoutes.tsx`: right-side detail pane routing for chat threads, post details, wallet overview, assets, receive, transfer, swap, keys, and transfer history
- `panes/`: concrete implementations for each desktop pane

The desktop home feed currently includes:

- Local
- Updates
- Topics
- Mutual Aid
- Groups
- People

---

## Wallets, Assets, and Transfers

The current transfer-chain and asset definitions are centralized in `packages/shared/src/transfer-assets.ts`. Both mobile and server share the same `chainId` / `assetId` definitions.

### Supported Chains

- Ethereum
- Bitcoin
- Solana
- Solana Devnet
- BNB Smart Chain
- Tron

### Supported Assets

- Ethereum: ETH, USDT, USDC
- Bitcoin: BTC
- Solana: SOL, USDT, USDC
- Solana Devnet: SOL, USDT
- BNB Smart Chain: BNB
- Tron: TRX, USDT

Asset display and transfers are related but not identical flows:

- The assets page queries asset snapshots for chain addresses through `/api/wallet/assets/*`; it currently covers `ethereum`, `bitcoin`, `solana`, `solana-devnet`, `bsc`, and `tron`
- The transfer page uses shared asset definitions to constrain selectable chains and assets, and performs chain-specific address validation, amount precision validation, and local signing
- Transfer history is written to a local secure database; it currently stores broadcasted outgoing transfers, including chain name, asset name, recipient address, transaction hash, and explorer link

### Transfer Flow

- The client selects chain, asset, recipient address, and amount, and validates address format based on chain type
- The client calls `/api/wallet/transfer/prepare`, and the server returns the unsigned transaction or the chain-specific transfer context to be signed
- The client signs using the locally derived wallet private key and never sends the private key to the server
- The client submits the signed transaction to `/api/wallet/transfer/broadcast` for broadcasting
- After a successful broadcast, the client constructs the explorer transaction URL for the given chain and writes the outgoing transfer into local transfer history

Server-side chain configuration is located in `server/.env`:

- Uses Alchemy RPC nodes
- `ALCHEMY_API_KEY=YourKey`

---

## Post Design Mechanism

The current post system revolves around six channels. Four are content channels, and two are directory channels:

- Content channels: Local, Updates, Topics, Mutual Aid
- Directory channels: Groups, People

These two directory channels do not participate in normal post distribution. Instead, they use directory-oriented data sources for group discovery and people discovery.

This posting system is intentionally not organized around KOLs, follower counts, or centralized recommendation slots. Instead, it aims to put the content itself and real user commitment back at the center of ranking signals.

Posts follow these product rules:

- Every post requires an author, channel, title, and body
- Both title and body are normalized with control-character cleaning, whitespace normalization, and length limits
- Current shared input limits are:
  - Title: up to 120 characters
  - Body: up to 2000 characters
  - Comment: up to 180 characters
- Posts can be text-only or include a cover image
- Media uses a separate upload-confirmation flow
- Post records store references such as `imagePath` and `imageUrl`
- The post detail page includes body, cover image, author profile, like count, comment count, blaze status, and on-chain session information

### Session-Based Feed Distribution

This is one of the current feed system’s core implementations:

- Only the four content channels enter the session feed
- The `Groups` and `People` directory channels bypass this distribution plan entirely
- The backend generates a `sessionId` and stable `planIds` for a continuous browsing session
- Within the same session, pagination will not repeat the same post
- Each page mixes boosted-hot content, boosted-pool content, recirculated content, and natural content in layered composition rather than a single chronological timeline
- When one layer lacks inventory, the system fills it with neighboring layers, such as using boosted content to fill natural slots or natural content to fill recirculation slots, prioritizing complete pages
- An explicit user refresh requests a new session plan, so refreshed results can change, while pagination order inside a session remains stable

### Post Interactions

Post interaction is currently divided into two layers:

- Light interactions already shipped: likes and comments
- Likes are deduplicated locally on the client, then written back to the server for counting
- Successful comments also increment the post’s comment count
- The post data model also keeps `likes`, `comments`, `saves`, and `shares` to support a fuller future interaction model
- The “Fuel” action on the detail page directly increases blaze-related counts and updates on-chain session state, making it a stronger visibility signal than a normal like

### On-Chain Boosting: Tempered Blaze

Posts are also connected to an on-chain boosting mechanism called Tempered Blaze, one of the core parts of the content feed:

- The goal is not traditional traffic buying, but a design closer to Superchat + Polymarket, where users spend real money to vote for content worth being seen, reshaping how valuable content is distributed
- During post creation, users can choose a `start mode`; before the post is created, the app first creates the corresponding blaze session on-chain, then writes `sessionId`, `sessionAddress`, `txHash`, and related data back into the post record
- There are currently four start modes:
  - 10 COAL / 10 minutes
  - 10 USDT / 10 minutes
  - 100 USDT / 60 minutes
  - 1000 USDT / 240 minutes
- Creators can publish using either USDT or COAL, making posting itself part of an on-chain capital-participation ecosystem rather than a pure cost sink
- Both server and client treat blaze fields as first-class post metadata, including:
  - `blazeSessionId`
  - `blazePoolTotalAtomic`
  - `blazeStartMode`
  - `blazeSettled`
  - `blazeCreatorAddress`
  - `blazeSessionAddress`
  - `blazeTxHash`
- In distribution, unsettled blaze posts with higher pool totals are prioritized into hot slots
- Settled blaze posts enter the recirculation layer rather than disappearing from the feed immediately
- COAL is not just a posting medium, but also part of the incentive loop:
  - users who fuel sessions can earn COAL rewards
  - users who participate in settlement can also earn rewards
  - creators themselves receive direct financial feedback
- Together, this forms a positive feedback loop for multiple participants
- On the detail page, users can continue fueling a session
- The client reads on-chain session state, syncs pool totals, and displays session ID, tier, transaction links, and current progress state

---

## Relationship Network Mechanism

The relationship network is neither an address book nor a “who-followed-whom” social graph. It is centered on address-based identities and gradually projects on-chain interactions and shared in-app context into an interpretable relationship graph.

The current rules are:

- Each user builds a relationship perspective around a subject address
- The system allows Ethereum, Solana, Tron, Bitcoin, and other multi-chain addresses to be gradually merged into the same relationship subject
- The smallest unit of the graph is not a single wallet address, but a merged relationship subject
- This lets users see something closer to real people rather than fragmented addresses
- Relationship signals come from both on-chain transfers and shared chat context
- It does not depend on any single behavior to determine closeness, but uses real exchange and shared context together to measure relationship strength
- Chat is protected by end-to-end encryption
- The server only receives ciphertext and the materials necessary for delivery and sync, not private-message plaintext
- Device keys and local decryption material are stored in secure local device storage and are not uploaded to the server
- The network view prioritizes the most meaningful core relationships instead of flattening every address at once
- The goal is interpretability, not raw data dumping

### Two Core Axes

Relationship calculation is currently organized around two primary axes:

- `Pulse`: represents recent state, focusing on whether there has been recent interaction, whether the relationship is heating up, and whether the connection is still “alive” now
- `Resonance`: represents long-term accumulation, focusing on whether the relationship has been validated over enough time, two-way exchanges, and real behavior to become structurally stable

On the on-chain side, the model considers:

- transfer frequency
- two-way exchange
- transaction amount scale
- active days
- penalties for hedging-like round-trip behavior

On the in-app side, the model considers:

- shared conversations
- message frequency
- active days
- number of dialogue rounds
- whether both parties continue appearing in the same discussion context

The final output is not a pile of raw stats, but product-readable relationship states, role labels, and explanatory copy.

### Layered Relationship Expression

To make the graph easier to understand, relationships are mapped into layered expressions:

- `Pulse` is interpreted as states such as “heating up,” “recently active,” “stable exchange,” “light interaction,” or “temporarily cooling”
- `Resonance` is interpreted as “highly verified,” “stably established,” “forming,” “weak connection,” or “initial signal”
- Node size is layered into `hero`, `major`, `minor`, and `tiny`, visually emphasizing truly core relationships rather than flattening all nodes equally
- In addition to layer labels, each node also carries `roleLabel`, `blurb`, and `sharedThread`, telling the user whether the connection is more like an inner-circle bond, a financially verified connection, or a conversation-based connection, and what shared context they were recently active around

### Asynchronous Refresh Mechanism

Relationship graph refresh uses an asynchronous update model:

- The graph is not recalculated synchronously every time the page opens
- Instead, it is maintained through background refresh tasks to balance responsiveness and computation cost
- The system records refresh state per relationship subject, including `idle`, `pending`, `in_progress`, and `failed`
- It also maintains freshness, error information, and the next eligible refresh time
- When the client detects that the graph is stale, it requests `ensure-fresh`
- The actual recomputation is handled by the backend refresh queue and worker, avoiding repeated recomputation from frequent page visits
- If a subject is already refreshing, still in a cooldown window, or was just refreshed, the system automatically skips or rate-limits the request to keep the queue stable and manageable
- Failed refreshes do not immediately erase the existing graph
- Instead, the system preserves the old result, error state, and retry opportunity, prioritizing readability over an all-or-nothing outcome

### Product Experience Goal

From a product-experience perspective, the relationship network emphasizes relationship interpretation rather than raw transaction display:

- What users see is a curated map of circles, roles, shared threads, and explanatory copy, not bare transaction details
- Relationships with recent chat or transfer activity gain a stronger sense of presence, helping users quickly identify which connections are still alive and which are only historical residue
- The graph is not meant to prove “I know a lot of people,” but to help users understand which relationships are real, stable, still warming up, and why they matter
- Contact profiles, chat context, and the graph complement one another so that address-based identity gradually grows into a readable, perceptible, and cultivatable human network

---

## Main Service Modules

- Health check: provides `/api/health`, aggregating API state, worker state, queue depth, and recent error info
- Feed and posts: handles feed reads, session distribution, post detail, post creation, media upload, comments, and like / blaze-related counters
- Profile and people: handles profile data, contact profiles, avatar and tag information, and linked display between posts and people profiles
- Chat and private groups: handles conversations, messages, device registration, E2EE state, private-group key distribution / rotation, member join and removal, and related capabilities
- Relationship network: handles multi-chain address merging, on-chain transfer relationship ingestion, relationship graph projection refresh, relationship queries, and refresh queue management
- Wallets, assets, and broadcasting: handles multi-chain asset snapshots, unsigned transaction preparation, signed transaction broadcasting, and transfer flow support
- Notifications: handles notification data and notification list capabilities
- Other supporting modules: currently also includes `auth`, `life-tree`, `ai`, `database`, and others for authentication placeholders, timeline capability, AI entrypoints, and low-level database connections

---

## More Documentation

- `docs/architecture.md`: the earliest architecture draft and product pillars, useful for understanding the project’s starting point
- `supabase/migrations`: migration history for database structure, relationship network, feed, chat E2EE, worker runtime, and related capabilities
- `packages/shared/src/index.ts`: shared cross-platform types, including core data structures for feed, chat, relationship network, and more
- `packages/shared/src/transfer-assets.ts`: shared multi-chain transfer `chainId` / `assetId` definitions and explorer configuration
- `mobile/src/features`: main mobile feature entrypoints, a good place to continue reading through feed, chat, network, and contacts
- `mobile/src/desktop`: desktop routing, panes, and desktop interaction layer for macOS
- `server/src/modules`: backend business modules organized by `feed`, `posts`, `chat`, `relationship-network`, `wallet-broadcast`, and related capabilities

If you want, I can also turn this into a more polished English project overview for docs, pitch decks, or a README-style introduction.
