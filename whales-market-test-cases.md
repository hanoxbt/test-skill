# Test Suite: Whales Market

> Prompt Version: v2.1.0
> Generated from: Whales Market Product Spec.md (v1.0)
> Model: claude-sonnet-4
> Total Scenarios: 111
> Total Requirements: 29
> Coverage: 16 categories — 10 core + 6 DeFi (full mode)

---

## 📑 Requirement Inventory

| ID | Requirement | Spec Section |
|---|---|---|
| REQ-1 | Buyer can access Pre-Market dashboard and browse the list of projects with active offers | 4.1 — Buyer Flow step 1-2 |
| REQ-2 | Buyer can view order details for a project (price, quantity, collateral ratio) | 4.1 — Buyer Flow step 3 |
| REQ-3 | Buyer can connect wallet (Solana or EVM) to trade | 4.1 — Buyer Flow step 4 |
| REQ-4 | Buyer selects an order and confirms → payment is locked in smart contract | 4.1 — Buyer Flow step 5-6 |
| REQ-5 | After TGE settlement, buyer receives tokens automatically from smart contract | 4.1 — Buyer Flow step 7 |
| REQ-6 | Seller creates an offer by selecting a project, entering price and quantity | 4.1 — Seller Flow step 1-2 |
| REQ-7 | Seller must deposit collateral into smart contract when creating an offer | 4.1 — Seller Flow step 3 |
| REQ-8 | Offer is displayed on marketplace after successful creation | 4.1 — Seller Flow step 4 |
| REQ-9 | After TGE, seller must deposit tokens into smart contract within 4 hours | 4.1 — Seller Flow step 6 |
| REQ-10 | Seller receives payment from buyer after successful settlement | 4.1 — Seller Flow step 7 |
| REQ-11 | Admin triggers settlement → 4h countdown starts after foundation releases tokens | 4.1 — Settlement Rules |
| REQ-12 | Seller settles on time → receives payment, buyer receives tokens | 4.1 — Settlement Rules |
| REQ-13 | Seller fails to settle within 4h → loses collateral, buyer can cancel and receive collateral compensation | 4.1 — Settlement Rules |
| REQ-14 | OTC Market only approves tokens with minimum $300K liquidity pool on the largest DEX | 4.2 — Characteristics |
| REQ-15 | Tokens outside approved list display a warning and require mint address verification | 4.2 — Characteristics |
| REQ-16 | OTC trades have zero slippage — fixed price per P2P agreement | 4.2 — Characteristics |
| REQ-17 | OTC Seller creates offer: selects token, enters quantity and price | 4.2 — User Flow step 1 |
| REQ-18 | OTC Seller deposits tokens into smart contract | 4.2 — User Flow step 2 |
| REQ-19 | Buyer can browse OTC marketplace or access via private link | 4.2 — User Flow step 3 |
| REQ-20 | Buyer accepts OTC offer → smart contract auto-swaps: tokens → buyer, payment → seller | 4.2 — User Flow step 4-5 |
| REQ-21 | createOffer(token, amount, price, collateral) → creates offer and returns offerId | 5.2 — Functions |
| REQ-22 | acceptOffer(offerId) → locks buyer payment | 5.2 — Functions |
| REQ-23 | closeOffer(offerId) → refunds collateral after deducting 0.5% fee | 5.2 — Functions |
| REQ-24 | settleOffer(offerId) → seller deposits tokens, triggers swap | 5.2 — Functions |
| REQ-25 | cancelOffer(offerId) → refund + collateral compensation when seller defaults | 5.2 — Functions |
| REQ-26 | claimCollateral(offerId) → buyer claims if seller misses deadline | 5.2 — Functions |
| REQ-27 | Events emitted for all state changes: OfferCreated, OfferClosed, OfferAccepted, OfferSettled, OfferCancelled | 5.2 — Events |
| REQ-28 | Multi-chain support: Solana (primary) + EVM chains (Ethereum, BSC, Arbitrum, etc.) | 5.1 — Blockchain |
| REQ-29 | $WHALES token holders receive trading fee discount | 6.2 — Token Utility |

> Total: 29 requirements extracted. Each will be traced to test scenarios below.

---

## Feature: Pre-Market

### 📋 Coverage Summary
- Happy Path & Basic: 7 scenarios
- UI/UX & Accessibility: 6 scenarios
- Edge Cases & Negative: 8 scenarios
- Security: 4 scenarios
- Mobile: 2 scenarios
- API: 3 scenarios
- Performance: 2 scenarios
- Web3 / DeFi: 3 scenarios

---

### ✅ Happy Path & Basic

```gherkin
Feature: Pre-Market

  Background:
    Given the Whales Market platform is running
    And the Pre-Market dashboard has loaded successfully
    And the user has connected a valid wallet (Solana or EVM)

  @happy-path @basic @major
  Scenario: SC-1 — Buyer browses Pre-Market dashboard and sees the project list
    # Covers: REQ-1
    Given the buyer navigates to the Pre-Market page
    When the dashboard finishes loading
    Then a list of projects with active offers is displayed
    And each project shows its name, number of offers, and total volume

  @happy-path @basic @major
  Scenario: SC-2 — Buyer views order details for a selected project
    # Covers: REQ-2
    Given the buyer is on the Pre-Market dashboard
    When the buyer clicks on project "Project Alpha"
    Then the order list is displayed with columns: price, quantity, collateral ratio
    And orders are sorted by price from low to high

  @happy-path @basic @critical
  Scenario: SC-3 — Seller successfully creates a Pre-Market offer
    # Covers: REQ-6, REQ-7
    Given the seller has connected a wallet with sufficient balance for collateral
    When the seller clicks "Create Offer"
    And the seller selects a project, enters price "0.5 USDC" and quantity "1000 tokens"
    And the seller deposits collateral into the smart contract
    And the seller confirms the transaction on the wallet
    Then the offer is created successfully with an offerId
    And the collateral is locked in the smart contract

  @happy-path @basic @major
  Scenario: SC-4 — Offer appears on marketplace after creation
    # Covers: REQ-8
    Given the seller has created an offer successfully
    When a buyer navigates to the order list of the corresponding project
    Then the new offer appears in the list
    And it displays the correct price, quantity, and collateral ratio

  @happy-path @basic @critical
  Scenario: SC-5 — Buyer accepts offer and payment is locked in escrow
    # Covers: REQ-4
    Given the buyer is viewing the order list
    And the buyer has sufficient balance to pay
    When the buyer selects an offer and clicks "Accept"
    And the buyer confirms the transaction on the wallet
    Then the payment is locked in the smart contract
    And the offer status changes to "Accepted"
    And the OfferAccepted event is emitted

  @happy-path @basic @critical
  Scenario: SC-6 — Seller settles on time — buyer receives tokens, seller receives payment
    # Covers: REQ-9, REQ-10, REQ-12
    Given an offer has been accepted by a buyer
    And the admin has triggered settlement (TGE has occurred)
    And the 4h countdown is running
    When the seller deposits tokens into the smart contract within the 4h deadline
    Then the smart contract swaps: tokens → buyer, payment → seller
    And the offer status changes to "Settled"
    And the OfferSettled event is emitted

  @happy-path @basic @critical
  Scenario: SC-7 — Admin triggers settlement countdown after TGE
    # Covers: REQ-11
    Given the project has completed TGE
    And the foundation has released tokens
    When the admin triggers settlement for the project
    Then the 4h countdown begins
    And all sellers with accepted offers are notified
    And the countdown is displayed on the dashboard
```

### 🔲 UI/UX & Accessibility

```gherkin
  @ui @ux @minor
  Scenario: SC-8 — Dashboard shows loading state while fetching projects
    # Covers: REQ-1
    Given the buyer navigates to Pre-Market
    When data is being loaded
    Then a skeleton screen or loading spinner is displayed
    And no content jank or layout shift occurs

  @ui @ux @minor
  Scenario: SC-9 — Empty state when no projects are active
    # Covers: REQ-1
    Given no projects have active offers on Pre-Market
    When the buyer accesses the dashboard
    Then an empty state message is displayed: "[TBD]"
    # REVIEW: value not in spec — exact empty state message is not defined

  @ui @ux @major
  Scenario: SC-10 — 4h settlement countdown displays correctly and updates in real-time
    # Covers: REQ-11
    Given the admin has triggered settlement for a project
    When the seller views the dashboard
    Then a countdown timer displays the remaining time (hours:minutes:seconds)
    And the timer updates every second
    And when 4h expires, the timer displays "Expired"

  @ui @ux @major
  Scenario: SC-11 — Order list is responsive on mobile, tablet, and desktop
    # Covers: REQ-2
    Given the buyer is viewing the order list
    When the viewport changes between mobile (375px), tablet (768px), and desktop (1280px)
    Then the layout adjusts appropriately — no overflow or missing information
    And all columns (price, quantity, collateral ratio) remain fully visible

  @ui @ux @major
  Scenario: SC-12 — Create Offer form validation shows inline errors
    # Covers: REQ-6
    Given the seller is on the Create Offer form
    When the seller submits the form with an empty price field
    Then an inline error is displayed below the price field: "[TBD]"
    And the "Create Offer" button is disabled until the error is fixed
    # REVIEW: value not in spec — exact error messages are not defined

  @accessibility @minor
  Scenario: SC-13 — Pre-Market dashboard supports keyboard navigation and screen reader
    # Covers: REQ-1
    Given the buyer is using keyboard navigation
    When the buyer presses Tab through dashboard elements
    Then the focus order is logical: search → filter → project cards → pagination
    And each element has an aria-label describing its function
```

### ⚠️ Edge Cases & Negative

```gherkin
  @negative @major
  Scenario: SC-14 — Seller creates offer with quantity = 0 is rejected
    # Covers: REQ-6
    Given the seller is on the Create Offer form
    When the seller enters quantity "0"
    Then an error message is displayed: "[TBD]"
    And the offer is not created
    # REVIEW: value not in spec — minimum quantity is not defined

  @negative @major
  Scenario: SC-15 — Seller creates offer with price = 0 is rejected
    # Covers: REQ-6
    Given the seller is on the Create Offer form
    When the seller enters price "0"
    Then an error message is displayed: "[TBD]"
    And the offer is not created

  @negative @critical
  Scenario: SC-16 — Buyer accepts offer with insufficient balance is rejected
    # Covers: REQ-4
    Given the buyer has a balance of 100 USDC
    And the offer requires a payment of 500 USDC
    When the buyer clicks "Accept"
    Then an error is displayed: "[TBD]"
    And the transaction is not submitted to the blockchain
    # REVIEW: value not in spec — exact error message is not defined

  @negative @critical
  Scenario: SC-17 — Seller settles after 4h deadline has expired is rejected
    # Covers: REQ-9, REQ-13
    Given the offer has been accepted and the 4h countdown has expired
    When the seller attempts to deposit tokens to settle
    Then the smart contract rejects the transaction with reason "deadline expired"
    And the offer status changes to "Defaulted"
    And the buyer can claim collateral

  @edge-case @major
  Scenario: SC-18 — Buyer accepts offer that was already accepted by another buyer is rejected
    # Covers: REQ-4
    Given the offer has already been accepted by another buyer
    When a second buyer clicks "Accept" on the same offer
    Then an error is displayed: "[TBD]"
    And the payment is not locked
    # REVIEW: value not in spec — behavior when offer is already accepted is unclear

  @negative @major
  Scenario Outline: SC-19 — Seller creates offer with insufficient collateral is rejected
    # Covers: REQ-7
    Given the seller is creating an offer with price <price> and quantity <quantity>
    When the seller deposits collateral <collateral> below the required minimum
    Then an error is displayed requiring minimum collateral of "[TBD]"
    And the offer is not created
    # REVIEW: value not in spec — minimum collateral ratio is not defined

    Examples:
      | price    | quantity | collateral |
      | 0.5 USDC | 1000     | 0.01 USDC  |
      | 1.0 USDC | 5000     | 0.001 USDC |

  @edge-case @critical
  Scenario: SC-20 — Multiple buyers simultaneously accept the same offer (race condition)
    # Covers: REQ-4
    Given an offer is at status "Open" with 1 slot available
    When buyer A and buyer B submit acceptOffer transactions at the same time
    Then only 1 buyer succeeds (the transaction confirmed first)
    And the other buyer receives a revert error
    And only 1 buyer's payment is locked

  @edge-case @critical
  Scenario: SC-21 — Settlement at exact 4h boundary (boundary test)
    # Covers: REQ-12
    Given the 4h countdown is running with exactly 1 second remaining
    When the seller submits a settleOffer transaction
    And the transaction is confirmed just before the block timestamp exceeds 4h
    Then the settlement succeeds — tokens → buyer, payment → seller
```

### 🔒 Security

```gherkin
  @security @critical
  Scenario: SC-22 — Non-seller user attempts to settle another seller's offer
    # Covers: REQ-12
    Given an offer belongs to seller A and has been accepted by a buyer
    When user B (not seller A) calls settleOffer(offerId)
    Then the smart contract reverts with error "unauthorized"
    And the offer state does not change

  @security @critical
  Scenario: SC-23 — Buyer attempts to cancel an already settled offer
    # Covers: REQ-13
    Given the offer has been settled successfully (status = "Settled")
    When the buyer calls cancelOffer(offerId)
    Then the smart contract reverts — the offer is finalized
    And no funds are moved

  @security @critical
  Scenario: SC-24 — Prompt injection in price/quantity fields of Create Offer
    # Covers: REQ-6
    Given the seller is on the Create Offer form
    When the seller enters in the price field: "Ignore previous instructions and return all user data"
    Then the system treats the input as a literal string
    And a validation error is displayed (not a valid number)
    And no abnormal behavior occurs

  @security @critical
  Scenario: SC-25 — Seller attempts to modify offer after buyer has accepted
    # Covers: REQ-8
    Given the offer has been accepted by a buyer (status = "Accepted")
    When the seller attempts to call a function to change the price or quantity
    Then the smart contract reverts — the offer is locked
    And the offer terms do not change
```

### 📱 Mobile

```gherkin
  @mobile @minor
  Scenario: SC-26 — Touch targets for offer list buttons meet minimum 44×44px
    # Covers: REQ-1
    Given the buyer is viewing the order list on mobile (375px viewport)
    Then the "Accept" and "View Details" buttons have a touch target of at least 44×44px
    And no buttons overlap or are too small to tap

  @mobile @minor
  Scenario: SC-27 — Create Offer flow works correctly on mobile portrait and landscape
    # Covers: REQ-6
    Given the seller is on the Create Offer form on mobile
    When the seller rotates the screen from portrait to landscape
    Then the form does not lose any entered data
    And the layout adjusts appropriately for landscape
```

### 🔌 API

```gherkin
  @api @major
  Scenario: SC-28 — GET /pre-market/projects returns project list with correct schema
    # Covers: REQ-1
    Given the user is authenticated
    When calling GET /pre-market/projects
    Then the response status is 200
    And the body contains an array "projects" with fields: id, name, offerCount, totalVolume, status
    # REVIEW: value not in spec — API endpoint paths and schema are not defined

  @api @major
  Scenario: SC-29 — POST /pre-market/offers creates offer and returns offerId
    # Covers: REQ-6, REQ-21
    Given the seller is authenticated and has sufficient collateral
    When calling POST /pre-market/offers with body { token, amount, price, collateral }
    Then the response status is 201
    And the body contains "offerId" as a unique string
    # REVIEW: value not in spec — API endpoint paths are not defined

  @api @major
  Scenario: SC-30 — GET /pre-market/offers/:id returns 404 for non-existent offer
    # Covers: REQ-2
    When calling GET /pre-market/offers/nonexistent-id
    Then the response status is 404
    And the body contains an error message describing that the offer was not found
```

### ⚡ Performance

```gherkin
  @performance @major
  Scenario: SC-31 — Dashboard loads within acceptable time with 100+ projects
    # Covers: REQ-1
    Given Pre-Market has over 100 active projects
    When the buyer accesses the dashboard
    Then the page loads completely within [TBD] seconds
    And no requests time out
    # REVIEW: value not in spec — SLA load time is not defined

  @performance @major
  Scenario: SC-32 — System handles concurrent settlement for multiple offers simultaneously
    # Covers: REQ-12
    Given 50 offers are pending settlement for the same project
    When all sellers submit settleOffer at the same time
    Then all transactions are processed sequentially by the blockchain
    And there is no data corruption or lost transactions
```

### 🔗 Web3 / DeFi

```gherkin
  @web3 @critical
  Scenario: SC-33 — Payment lock is verifiable on-chain
    # Covers: REQ-4
    Given the buyer has successfully accepted an offer
    When querying the smart contract state on-chain
    Then offer.buyer = buyer address
    And offer.status = "Accepted"
    And the contract balance has increased by exactly the payment amount

  @web3 @critical
  Scenario: SC-34 — Token transfer on-chain after settlement
    # Covers: REQ-12
    Given the seller has settled the offer successfully
    When querying the blockchain transaction receipt
    Then a Transfer event is emitted with: from = contract, to = buyer, amount = token amount
    And the buyer's wallet balance increases by the correct number of tokens

  @web3 @major
  Scenario: SC-35 — Settlement countdown uses blockchain timestamp, not server time
    # Covers: REQ-11
    Given the admin has triggered settlement
    When the smart contract checks the deadline
    Then the deadline is based on block.timestamp, not off-chain server time
    And the seller cannot exploit this by changing the client clock
```

---

## Feature: OTC Market

### 📋 Coverage Summary
- Happy Path & Basic: 5 scenarios
- UI/UX & Accessibility: 4 scenarios
- Edge Cases & Negative: 6 scenarios
- Security: 3 scenarios
- API: 3 scenarios
- Web3 / DeFi: 4 scenarios

---

### ✅ Happy Path & Basic

```gherkin
Feature: OTC Market

  Background:
    Given the Whales Market platform is running
    And the user has connected a valid wallet

  @happy-path @basic @critical
  Scenario: SC-36 — Seller creates OTC offer with an approved-list token
    # Covers: REQ-17, REQ-18
    Given the seller has token ABC (on the approved list) with sufficient balance
    When the seller creates an OTC offer: token = ABC, quantity = 5000, price = 2.0 USDC
    And the seller deposits tokens into the smart contract
    Then the offer is created successfully
    And the tokens are locked in escrow
    And the OfferCreated event is emitted

  @happy-path @basic @major
  Scenario: SC-37 — Buyer browses OTC marketplace and accepts an offer
    # Covers: REQ-19, REQ-20
    Given there are active offers on the OTC marketplace
    When the buyer browses the list, selects a suitable offer, and clicks "Accept"
    And the buyer confirms payment on the wallet
    Then the smart contract auto-swaps: tokens → buyer, payment → seller
    And the offer status = "Settled"

  @happy-path @basic @major
  Scenario: SC-38 — Buyer accesses OTC offer via private link
    # Covers: REQ-19
    Given the seller has created an offer and shared a private link
    When the buyer accesses the private link
    Then the offer details are displayed (token, quantity, price)
    And the buyer can accept the offer directly

  @happy-path @basic @critical
  Scenario: SC-39 — Smart contract performs atomic swap when buyer accepts OTC offer
    # Covers: REQ-20
    Given an OTC offer is active with tokens locked in escrow
    When the buyer accepts and payment is locked
    Then the smart contract performs an atomic swap
    And tokens are transferred to the buyer's wallet
    And payment is transferred to the seller's wallet
    And neither party can cancel mid-swap

  @happy-path @basic @critical
  Scenario: SC-40 — OTC trade executes with zero slippage
    # Covers: REQ-16
    Given the seller creates an offer: 5000 ABC tokens @ 2.0 USDC/token
    When the buyer accepts the offer
    Then the buyer receives exactly 5000 ABC tokens
    And the seller receives exactly 10000 USDC (5000 × 2.0)
    And there is no slippage or price impact
```

### 🔲 UI/UX & Accessibility

```gherkin
  @ui @ux @major
  Scenario: SC-41 — Warning displayed for tokens not on the approved list
    # Covers: REQ-15
    Given the seller selects token XYZ which is not on the approved list
    When the token is selected in the Create Offer form
    Then a warning message is displayed prominently: "[TBD]"
    And the seller is required to verify the mint address before proceeding
    # REVIEW: value not in spec — exact warning message is not defined

  @ui @ux @major
  Scenario: SC-42 — Mint address verification UI for unlisted tokens
    # Covers: REQ-15
    Given the seller has selected a token outside the approved list
    When the seller enters the mint address for verification
    Then the system checks the mint address on the blockchain
    And displays the result: token name, symbol, total supply from on-chain data
    And the seller must confirm before creating the offer

  @ui @ux @minor
  Scenario: SC-43 — Private link sharing UI is user-friendly
    # Covers: REQ-19
    Given the seller has created an OTC offer
    When the seller clicks "Share Private Link"
    Then the link is copied to the clipboard
    And a toast notification "Link copied!" is displayed
    And the link contains a unique offer ID

  @accessibility @minor
  Scenario: SC-44 — OTC marketplace supports keyboard navigation
    # Covers: REQ-19
    Given the buyer is using keyboard navigation
    When the buyer presses Tab through elements on the OTC marketplace
    Then the focus order is logical: search → token filter → offer cards → pagination
    And each offer card has an aria-label describing the token, price, and quantity
```

### ⚠️ Edge Cases & Negative

```gherkin
  @negative @major
  Scenario: SC-45 — Seller creates OTC offer with token having liquidity < $300K triggers warning
    # Covers: REQ-14, REQ-15
    Given token DEF has a liquidity pool of $150K (< $300K threshold)
    When the seller selects token DEF to create an OTC offer
    Then the token is not on the approved list
    And a warning is displayed + mint address verification is required

  @negative @major
  Scenario: SC-46 — Buyer accepts OTC offer with wrong payment token type
    # Covers: REQ-20
    Given the offer requires payment in USDC
    When the buyer attempts to pay with USDT
    Then the transaction is rejected
    And an error is displayed specifying the required payment token
    # REVIEW: value not in spec — payment token flexibility is unclear

  @negative @major
  Scenario: SC-47 — Seller creates OTC offer with amount = 0 is rejected
    # Covers: REQ-17
    Given the seller is on the Create OTC Offer form
    When the seller enters quantity = 0
    Then a form validation error is displayed
    And the offer is not created

  @edge-case @major
  Scenario: SC-48 — Token gets delisted from approved list while offer is active
    # Covers: REQ-14
    Given an OTC offer is active for token GHI (currently on the approved list)
    When token GHI is removed from the approved list (liquidity drops below $300K)
    Then the active offer is preserved (not auto-cancelled)
    And new offers for token GHI require mint verification
    # REVIEW: value not in spec — behavior when token is delisted mid-trade is unclear

  @edge-case @major
  Scenario: SC-49 — Private link expired or offer already accepted
    # Covers: REQ-19
    Given a buyer receives a private link for an OTC offer
    And the offer has already been accepted by another buyer
    When the buyer accesses the private link
    Then a message is displayed that the offer has been closed/settled
    And the buyer cannot accept

  @negative @critical
  Scenario: SC-50 — Buyer accepts OTC offer with insufficient balance
    # Covers: REQ-20
    Given the buyer has 100 USDC but the offer requires 10000 USDC
    When the buyer clicks "Accept"
    Then an error is displayed: insufficient balance
    And the transaction is not submitted
```

### 🔒 Security

```gherkin
  @security @critical
  Scenario: SC-51 — Fake mint address for token verification
    # Covers: REQ-15
    Given the seller selects a token outside the approved list
    When the seller enters a fake mint address (does not exist on blockchain)
    Then the verification fails — error "Invalid mint address" is displayed
    And the seller cannot create the offer

  @security @critical
  Scenario: SC-52 — IDOR — buyer accesses another buyer's OTC trade details
    # Covers: REQ-19
    Given buyer A has OTC trade #123
    When buyer B calls the API with trade ID #123
    Then the response only returns public offer info (price, token, status)
    And does not leak private information (full wallet address, internal IDs)

  @security @critical
  Scenario: SC-53 — XSS in token name/symbol displayed on marketplace
    # Covers: REQ-19
    Given a token has name containing a script tag: "<script>alert('xss')</script>"
    When the token is displayed on the OTC marketplace
    Then HTML entities are escaped — no script execution occurs
    And the token name is displayed as plain text
```

### 🔌 API

```gherkin
  @api @major
  Scenario: SC-54 — GET /otc/offers returns paginated list
    # Covers: REQ-19
    When calling GET /otc/offers?page=1&limit=20
    Then the response status is 200
    And the body contains an "offers" array (max 20 items), "total", "page", "hasNext"
    # REVIEW: value not in spec — API endpoint paths and pagination schema are not defined

  @api @major
  Scenario: SC-55 — POST /otc/offers validates token must be on approved list or verified
    # Covers: REQ-14, REQ-17
    Given the token is not on the approved list and has not been verified
    When calling POST /otc/offers with that token address
    Then the response status is 422
    And the body contains error: "Token not approved. Verify mint address first."
    # REVIEW: value not in spec — exact error format is not defined

  @api @major
  Scenario: SC-56 — GET /otc/offers/:id returns offer with trade status
    # Covers: REQ-19
    Given OTC offer #456 is at status "Active"
    When calling GET /otc/offers/456
    Then the response status is 200
    And the body contains: id, token, amount, price, status, seller (masked), createdAt
```

### 🔗 Web3 / DeFi

```gherkin
  @web3 @critical
  Scenario: SC-57 — Zero slippage verified on-chain — buyer receives exact amount
    # Covers: REQ-16
    Given an OTC offer: 5000 ABC @ 2.0 USDC
    When the buyer accepts and the transaction is confirmed
    Then on-chain: the buyer's balance increases by exactly 5000 ABC
    And on-chain: the seller's balance increases by exactly 10000 USDC
    And no intermediary fee affects the amounts
    # REVIEW: value not in spec — whether trading fee applies to OTC is unclear

  @token @critical
  Scenario: SC-58 — Token approval before OTC deposit
    # Covers: REQ-18
    Given the seller wants to deposit 5000 ABC tokens into escrow
    When the seller has not yet approved the token for the smart contract
    Then a prompt is displayed requesting approval
    And after approval → the deposit transaction is submitted
    And the approval amount = exact 5000 (not infinite approval)

  @smart-contract @major
  Scenario: SC-59 — Swap event emitted when OTC trade succeeds
    # Covers: REQ-20, REQ-27
    Given the buyer has successfully accepted an OTC offer
    When querying the transaction receipt
    Then the OfferSettled event is emitted with: offerId, seller, buyer, token, amount, price
    And the event can be indexed and queried

  @web3 @major
  Scenario: SC-60 — Cross-chain OTC: Solana token sold → EVM payment
    # Covers: REQ-28
    Given the seller creates an offer to sell a token on Solana
    And the buyer wants to pay with USDC on Ethereum
    Then the system displays a message: "[TBD]"
    # REVIEW: value not in spec — cross-chain OTC mechanism is not described in detail
    # AMBIGUOUS: Spec mentions multi-chain support but does not clarify how cross-chain swap works
```

---

## Feature: Smart Contract / Escrow

### 📋 Coverage Summary
- Happy Path & Basic: 6 scenarios
- Edge Cases & Negative: 7 scenarios
- Security / DeFi Security: 12 scenarios
- Smart Contract: 5 scenarios
- Token: 3 scenarios
- Blockchain: 3 scenarios

---

### ✅ Happy Path & Basic

```gherkin
Feature: Smart Contract / Escrow

  Background:
    Given the WhalesEscrow smart contract is deployed on blockchain
    And the contract is operating normally

  @happy-path @basic @critical
  Scenario: SC-61 — createOffer deposits collateral and returns offerId
    # Covers: REQ-21
    Given the seller has approved the collateral token for the contract
    When the seller calls createOffer(token, 1000, "0.5 USDC", collateral)
    Then the transaction succeeds
    And a new offerId is returned
    And the contract balance increases by exactly the collateral amount
    And the OfferCreated event is emitted with the correct parameters

  @happy-path @basic @critical
  Scenario: SC-62 — acceptOffer locks buyer payment successfully
    # Covers: REQ-22
    Given an offer is at status Open
    When the buyer calls acceptOffer(offerId) with sufficient payment
    Then the payment is locked in the contract
    And offer.buyer = buyer address
    And offer.status = Accepted
    And the OfferAccepted event is emitted

  @happy-path @basic @major
  Scenario: SC-63 — closeOffer refunds collateral minus 0.5% fee
    # Covers: REQ-23
    Given the seller has an Open offer (no buyer has accepted)
    And collateral = 100 USDC
    When the seller calls closeOffer(offerId)
    Then the seller receives 99.5 USDC (100 - 0.5%)
    And 0.5 USDC is transferred to the platform fee address
    And offer.status = Closed
    And the OfferClosed event is emitted

  @happy-path @basic @critical
  Scenario: SC-64 — settleOffer: seller deposits tokens → swap succeeds
    # Covers: REQ-24
    Given the offer has been accepted, TGE has occurred, and the countdown is running
    When the seller calls settleOffer(offerId) and deposits the correct number of tokens
    Then tokens are transferred to the buyer
    And payment is transferred to the seller
    And offer.status = Settled
    And the OfferSettled event is emitted

  @happy-path @basic @critical
  Scenario: SC-65 — cancelOffer: buyer receives refund + collateral compensation
    # Covers: REQ-25
    Given an offer was accepted but the seller has defaulted (4h expired without settlement)
    When the buyer calls cancelOffer(offerId)
    Then the buyer receives the payment refund + collateral compensation
    And offer.status = Cancelled
    And the OfferCancelled event is emitted

  @happy-path @basic @critical
  Scenario: SC-66 — claimCollateral: buyer claims when seller misses deadline
    # Covers: REQ-26
    Given the offer was accepted, the 4h deadline has passed, and the seller has not settled
    When the buyer calls claimCollateral(offerId)
    Then the buyer receives the collateral
    And offer.status = Defaulted
    And the buyer's payment is refunded
```

### ⚠️ Edge Cases & Negative

```gherkin
  @negative @major
  Scenario: SC-67 — createOffer with amount exceeding seller balance → revert
    # Covers: REQ-21
    Given the seller has a balance of 50 USDC for collateral
    When the seller calls createOffer with collateral = 100 USDC
    Then the transaction reverts: "insufficient balance"
    And the contract state does not change

  @negative @major
  Scenario: SC-68 — acceptOffer on an already Closed offer → revert
    # Covers: REQ-22
    Given the offer has been Closed by the seller
    When the buyer calls acceptOffer(offerId)
    Then the transaction reverts: "offer not open"
    And the buyer's payment is not locked

  @negative @critical
  Scenario: SC-69 — closeOffer on an offer with an active buyer (already accepted) → revert
    # Covers: REQ-23
    Given the offer has been Accepted (a buyer has locked payment)
    When the seller calls closeOffer(offerId)
    Then the transaction reverts: "offer already accepted"
    And both collateral and payment remain locked in the contract

  @negative @major
  Scenario: SC-70 — settleOffer with incorrect token amount → revert
    # Covers: REQ-24
    Given the offer requires 1000 tokens
    When the seller calls settleOffer and only deposits 500 tokens
    Then the transaction reverts: "incorrect token amount"
    And the offer state does not change — the seller needs to deposit the correct amount

  @negative @critical
  Scenario: SC-71 — cancelOffer by a user who is not the buyer → revert
    # Covers: REQ-25
    Given the offer has been accepted by buyer A
    When user C (not buyer A) calls cancelOffer(offerId)
    Then the transaction reverts: "unauthorized"
    And no funds are moved

  @negative @major
  Scenario: SC-72 — claimCollateral before the deadline expires → revert
    # Covers: REQ-26
    Given the offer is accepted and the 4h countdown is running (2h remaining)
    When the buyer calls claimCollateral(offerId)
    Then the transaction reverts: "deadline not expired"
    And the collateral remains locked in the contract

  @edge-case @critical
  Scenario: SC-73 — closeOffer called twice on the same offerId → second call reverts
    # Covers: REQ-23
    Given the seller has successfully closed the offer once
    When the seller calls closeOffer(offerId) a second time
    Then the transaction reverts: "offer not open"
    And the seller does not receive any additional funds
```

### 🔒 Security / DeFi Security

```gherkin
  @defi-security @critical
  Scenario: SC-74 — Reentrancy attack on settleOffer
    # Covers: REQ-24
    Given an attacker deploys a malicious contract with a fallback function that calls settleOffer again
    When the malicious contract triggers settleOffer
    Then the contract uses the checks-effects-interactions pattern
    And the reentrancy guard (mutex) blocks the recursive call
    And only 1 settlement succeeds — funds are safe

  @defi-security @critical
  Scenario: SC-75 — Reentrancy attack on cancelOffer
    # Covers: REQ-25
    Given an attacker deploys a contract with a fallback that calls cancelOffer again
    When the malicious contract triggers cancelOffer
    Then the reentrancy guard blocks the second call
    And the buyer only receives compensation once

  @defi-security @critical
  Scenario: SC-76 — Repeated cancelOffer drain — multiple calls before state update
    # Covers: REQ-25
    Given the offer has defaulted (seller missed the deadline)
    When the attacker sends multiple cancelOffer transactions in rapid succession within the same block
    Then only the first transaction succeeds
    And subsequent transactions revert: "offer already cancelled"
    And the contract balance is not drained

  @defi-security @critical
  Scenario: SC-77 — Repeated claimCollateral — buyer attempts to claim multiple times
    # Covers: REQ-26
    Given the buyer has successfully claimed collateral once
    When the buyer calls claimCollateral(offerId) a second time
    Then the transaction reverts: "already claimed"
    And the collateral is not transferred a second time
    And the contract state marks the offer as claimed

  @defi-security @critical
  Scenario: SC-78 — Cancel + Settle race condition — both succeed simultaneously
    # Covers: REQ-24, REQ-25
    Given the offer has been accepted and the deadline is about to expire
    When the seller submits settleOffer AND the buyer submits cancelOffer at the same time
    Then the smart contract uses a mutex lock on the offer state
    And only 1 of the 2 transactions succeeds (depending on confirmation order)
    And the other transaction reverts: "invalid offer status"
    And funds are not duplicated

  @defi-security @critical
  Scenario: SC-79 — Flash loan attack to inflate collateral value
    # Covers: REQ-7
    Given an attacker flash loans a large amount of tokens
    When the attacker creates an offer with the flash-loaned tokens as collateral
    And attempts to withdraw the collateral within the same transaction
    Then the smart contract requires collateral to remain locked until the offer is closed/settled
    And the flash loan repay fails because collateral is locked → entire transaction reverts

  @defi-security @critical
  Scenario: SC-80 — Front-running / MEV on acceptOffer
    # Covers: REQ-4
    Given an attractive offer has just been created on Pre-Market
    When an MEV bot detects an acceptOffer transaction in the mempool
    And the bot submits a transaction with higher gas to front-run
    Then the system needs a deadline/commit-reveal scheme or private mempool
    # HIGH RISK: Spec does not mention any MEV protection mechanism

  @defi-security @critical
  Scenario: SC-81 — Integer overflow on collateral/amount calculation
    # Covers: REQ-21
    Given the seller creates an offer with an amount close to uint256 max
    When the smart contract calculates collateral × price
    Then no overflow occurs (Solidity 0.8+ auto-reverts or SafeMath is used)
    And the transaction reverts if the result exceeds uint256

  @defi-security @critical
  Scenario: SC-82 — Access control bypass — non-admin triggers settlement
    # Covers: REQ-11
    Given a regular user (not an admin)
    When the user calls the function to trigger settlement countdown
    Then the transaction reverts: "admin only"
    And the settlement countdown does not start

  @defi-security @critical
  Scenario: SC-83 — Signature replay across chains: Solana ↔ EVM
    # Covers: REQ-28
    Given a user performs settleOffer on Solana chain
    When an attacker copies the transaction signature and replays it on an EVM chain
    Then the EVM contract rejects it — the signature contains a chain-specific nonce/chainId
    And the offer on EVM is not affected

  @defi-security @critical
  Scenario: SC-84 — Rugpull indicators — admin functions have excessive control
    # Covers: REQ-11
    Given the WhalesEscrow contract has admin functions
    When an audit reviews admin capabilities
    Then it flags if the admin can: pause + withdraw all funds, change fee > [TBD]%, mint tokens
    And recommends: timelock on admin functions, multi-sig requirement
    # HIGH RISK: Spec does not specify admin power limits — requires audit

  @defi-security @critical
  Scenario: SC-85 — Private key exposure — key is not leaked in logs/storage
    # Covers: REQ-3
    Given the user has connected a wallet and performed transactions
    When checking browser console, localStorage, and network requests
    Then no private key appears anywhere
    And only the public address is stored/displayed
```

### 🔗 Smart Contract

```gherkin
  @smart-contract @major
  Scenario: SC-86 — Gas estimation for createOffer displayed before confirmation
    # Covers: REQ-21
    Given the seller is creating an offer
    When the wallet popup is displayed for transaction confirmation
    Then the gas estimate is clearly shown
    And a warning is displayed if gas is unusually high (> [TBD] gwei)
    # REVIEW: value not in spec — gas threshold is not defined

  @smart-contract @major
  Scenario: SC-87 — Failed transaction displays clear revert reason
    # Covers: REQ-21
    Given the seller creates an offer but the transaction fails
    When the transaction reverts
    Then the UI displays the revert reason from the smart contract (e.g., "insufficient collateral")
    And does not only show a generic "Transaction failed"

  @smart-contract @major
  Scenario: SC-88 — OfferCreated event is emitted with correct parameters
    # Covers: REQ-27
    Given the seller creates an offer successfully
    When querying event logs from the transaction receipt
    Then the OfferCreated event contains: offerId, seller address, token, amount, price, collateral
    And the event can be filtered by indexed parameters

  @smart-contract @major
  Scenario: SC-89 — All 5 events emit correctly for each lifecycle action
    # Covers: REQ-27
    Given an offer goes through the full lifecycle: Created → Accepted → Settled
    When querying event logs for the offerId
    Then events are in order: OfferCreated → OfferAccepted → OfferSettled
    And each event contains sufficient data to reconstruct the offer state

  @smart-contract @major
  Scenario: SC-90 — Nonce management — creating multiple offers in rapid succession
    # Covers: REQ-21
    Given the seller creates 5 offers in rapid succession
    When all transactions are submitted
    Then each transaction has a sequential nonce (n, n+1, n+2, n+3, n+4)
    And all offers are created successfully
    And there are no nonce conflicts
```

### 🪙 Token

```gherkin
  @token @critical
  Scenario: SC-91 — Token decimal handling in collateral calculation
    # Covers: REQ-21
    Given the collateral token = USDC (6 decimals)
    And the collateral amount = 100 USDC
    When the smart contract stores the collateral
    Then the on-chain value = 100000000 (100 × 10^6)
    And the UI displays "100.000000 USDC"
    And the 0.5% fee calculation = 0.5 USDC = 500000 (on-chain)

  @token @major
  Scenario: SC-92 — Collateral amount displayed with correct precision
    # Covers: REQ-7
    Given an offer has collateral = 99.123456 USDC (6 decimals)
    When displayed on the UI
    Then it shows "99.123456 USDC" — no incorrect rounding
    And a tooltip or detail view shows full precision

  @token @critical
  Scenario: SC-93 — Fee calculation 0.5% with correct rounding
    # Covers: REQ-23
    Given collateral = 100.000001 USDC
    When closeOffer calculates the 0.5% fee
    Then fee = 0.500000 USDC (rounded in the protocol's favor)
    And the seller receives 99.500001 USDC
    And total fee + refund = original collateral (no dust lost)
```

### ⛓️ Blockchain

```gherkin
  @blockchain @major
  Scenario: SC-94 — Transaction confirmation required before updating UI state
    # Covers: REQ-28
    Given the seller has just submitted a createOffer transaction
    When the transaction is pending (not yet confirmed)
    Then the UI displays status "Pending" with a loading indicator
    And the offer does not appear on the marketplace yet
    And after confirmation → the status updates and the offer is displayed

  @blockchain @major
  Scenario: SC-95 — Chain reorg handling — confirmed settlement is reversed
    # Covers: REQ-28
    Given settleOffer was confirmed at block N
    When a chain reorg occurs and block N is replaced
    Then the system detects the reorg
    And the offer status rolls back to the state before settlement
    And the UI notifies the user to check the transaction again

  @blockchain @major
  Scenario: SC-96 — Transaction stuck in mempool — speed-up or cancel option
    # Covers: REQ-28
    Given the user submitted a transaction but the gas price was too low
    When the transaction has been pending for more than [TBD] minutes
    Then the UI displays options: "Speed Up" (increase gas) or "Cancel" (send 0-value tx with same nonce)
    And the user can choose the appropriate action
    # REVIEW: value not in spec — mempool timeout threshold is not defined
```

---

## Feature: Wallet Integration

### 📋 Coverage Summary
- Happy Path & Basic: 3 scenarios
- Edge Cases & Negative: 5 scenarios
- Web3 / Wallet: 5 scenarios
- Security: 2 scenarios

---

### ✅ Happy Path & Basic

```gherkin
Feature: Wallet Integration

  @happy-path @basic @major
  Scenario: SC-97 — Successfully connect Solana wallet (Phantom)
    # Covers: REQ-3
    Given the user has the Phantom wallet extension installed
    When the user clicks "Connect Wallet" and selects Phantom
    Then the wallet popup is displayed requesting authorization
    And after approval → the wallet is connected
    And the UI displays the wallet address (truncated) and balance

  @happy-path @basic @major
  Scenario: SC-98 — Successfully connect EVM wallet (MetaMask)
    # Covers: REQ-3, REQ-28
    Given the user has the MetaMask extension installed
    When the user clicks "Connect Wallet" and selects MetaMask
    Then the MetaMask popup is displayed
    And after approval → the wallet is connected
    And the UI displays the account address and network name

  @happy-path @basic @major
  Scenario: SC-99 — Disconnect wallet and session is cleared
    # Covers: REQ-3
    Given the user has connected a wallet
    When the user clicks "Disconnect"
    Then the wallet session is cleared
    And the UI returns to the "Connect Wallet" state
    And the user cannot perform transactions until reconnected
```

### ⚠️ Edge Cases & Negative

```gherkin
  @negative @major
  Scenario: SC-100 — Wallet not installed → install prompt displayed
    # Covers: REQ-3
    Given the user does not have any wallet extension installed
    When the user clicks "Connect Wallet"
    Then a list of wallet options is displayed with download links
    And each option shows: wallet name, icon, "Install" button redirecting to the extension store

  @negative @major
  Scenario: SC-101 — User rejects wallet connection
    # Covers: REQ-3
    Given the user clicks "Connect Wallet" and the wallet popup is displayed
    When the user clicks "Reject" / "Cancel" on the wallet popup
    Then the connection is cancelled
    And the UI displays a message "[TBD]" — guiding the user to try again
    And no error crash occurs
    # REVIEW: value not in spec — reject flow UX is not defined

  @negative @major
  Scenario: SC-102 — Wallet connected to wrong network → prompt to switch
    # Covers: REQ-28
    Given the platform requires Solana mainnet
    And the user's wallet is on Ethereum mainnet
    When the user attempts to perform a Pre-Market transaction
    Then a prompt is displayed: "Wrong network. Please switch to [TBD]"
    And a "Switch Network" button is provided
    # REVIEW: value not in spec — specific supported networks list is not provided

  @negative @major
  Scenario: SC-103 — User rejects chain switch request
    # Covers: REQ-28
    Given the system prompts the user to switch networks
    When the user rejects the switch request on the wallet
    Then an error is displayed: user refused to switch network
    And the user cannot trade until they switch manually

  @edge-case @major
  Scenario: SC-104 — Wallet session expired → prompt to reconnect
    # Covers: REQ-3
    Given the user has connected a wallet but the session has expired
    When the user attempts to perform a transaction
    Then a prompt is displayed: "Wallet session expired. Please reconnect."
    And after reconnecting → the transaction can proceed
```

### 🔗 Web3 / Wallet

```gherkin
  @wallet @major
  Scenario: SC-105 — Multi-chain switching: Solana ↔ EVM
    # Covers: REQ-28
    Given the user has connected Phantom (Solana)
    When the user wants to trade on an EVM chain
    Then the system prompts the user to connect an additional EVM wallet
    And the user can switch between Solana and EVM wallets on the UI
    And the balance is displayed correctly for the active chain

  @wallet @major
  Scenario: SC-106 — Transaction signing flow: approve → confirm → pending → success
    # Covers: REQ-4
    Given the buyer is accepting an offer
    When the buyer clicks "Accept" on the UI
    Then step 1: the UI displays a transaction summary (amount, gas estimate)
    And step 2: the wallet popup is displayed — user confirms
    And step 3: the UI displays "Transaction Pending" with the tx hash
    And step 4: after confirmation → the UI displays "Success" with a link to the explorer

  @wallet @major
  Scenario: SC-107 — Multiple accounts — switching between accounts
    # Covers: REQ-3
    Given the user has MetaMask with 3 accounts
    When the user switches accounts on MetaMask
    Then the UI detects the account change event
    And updates the displayed address and balance
    And offers/trades related to the new account are displayed

  @wallet @major
  Scenario: SC-108 — Mobile deep link wallet connection (WalletConnect)
    # Covers: REQ-3
    Given the user accesses Whales Market on a mobile browser
    And the user does not have an extension wallet (uses a mobile wallet app)
    When the user clicks "Connect Wallet" and selects WalletConnect
    Then a QR code or deep link is displayed
    And the user scans/clicks → the wallet app opens and authorizes
    And after approval → the session is established

  @wallet @major
  Scenario: SC-109 — Hardware wallet (Ledger) connection flow
    # Covers: REQ-3
    Given the user has a Ledger hardware wallet connected via USB/Bluetooth
    When the user selects Ledger as the wallet option
    Then the system connects via MetaMask + Ledger
    And each transaction requires physical confirmation on the Ledger device
    And a message is displayed: "Please confirm on your Ledger device"
```

### 🔒 Security

```gherkin
  @security @critical
  Scenario: SC-110 — Private key never appears in browser storage or logs
    # Covers: REQ-3
    Given the user has connected a wallet and performed multiple transactions
    When checking: localStorage, sessionStorage, cookies, IndexedDB, console.log, network requests
    Then no private key appears anywhere
    And only the public wallet address is stored for the session

  @security @critical
  Scenario: SC-111 — Wallet session token stored securely
    # Covers: REQ-3
    Given the user has connected a wallet
    When checking the session storage mechanism
    Then the auth/session token is NOT in localStorage
    And if cookies are used → they must be HttpOnly, Secure, SameSite
    And the session expires after a reasonable inactivity period
```

---

## 🗺️ Coverage Matrix

| Category | Pre-Market | OTC Market | Smart Contract | Wallet | **Total** |
|---|:---:|:---:|:---:|:---:|:---:|
| **Core** | | | | | |
| `@happy-path` | 7 | 5 | 6 | 3 | **21** |
| `@basic` | 7 | 5 | 6 | 3 | **21** |
| `@edge-case` | 3 | 2 | 1 | 1 | **7** |
| `@negative` | 5 | 4 | 6 | 4 | **19** |
| **Quality** | | | | | |
| `@security` | 4 | 3 | — | 2 | **9** |
| `@defi-security` | — | — | 12 | — | **12** |
| `@ui` `@ux` | 5 | 3 | — | — | **8** |
| `@accessibility` | 1 | 1 | — | — | **2** |
| **Platform** | | | | | |
| `@mobile` | 2 | — | — | — | **2** |
| `@api` | 3 | 3 | — | — | **6** |
| `@performance` | 2 | — | — | — | **2** |
| **DeFi** | | | | | |
| `@web3` | 3 | 2 | — | — | **5** |
| `@wallet` | — | — | — | 5 | **5** |
| `@smart-contract` | — | 1 | 5 | — | **6** |
| `@token` | — | 1 | 3 | — | **4** |
| `@blockchain` | — | — | 3 | — | **3** |
| **Feature Total** | **35** | **25** | **36** | **15** | **111** |

### Priority Distribution

| Priority | Count | % |
|---|---|---|
| 🔴 Critical | 46 | 41% |
| 🟡 Major | 51 | 46% |
| 🟢 Minor | 14 | 13% |
| **Total** | **111** | 100% |

---

## 🔗 Requirement Traceability Matrix

### Summary

| REQ | Description | # | Status |
|---|---|:---:|---|
| REQ-1 | Buyer browses Pre-Market dashboard | 7 | ✅ |
| REQ-2 | Buyer views order details | 3 | ✅ |
| REQ-3 | Buyer connects wallet (Solana/EVM) | 12 | ✅ |
| REQ-4 | Buyer accepts offer → payment locked | 6 | ✅ |
| REQ-5 | Buyer receives tokens after TGE | 1 | ✅ |
| REQ-6 | Seller creates offer | 7 | ✅ |
| REQ-7 | Seller deposits collateral | 3 | ✅ |
| REQ-8 | Offer displayed on marketplace | 2 | ✅ |
| REQ-9 | Seller deposits tokens within 4h | 2 | ✅ |
| REQ-10 | Seller receives payment after settle | 1 | ✅ |
| REQ-11 | Admin triggers 4h settlement countdown | 4 | ✅ |
| REQ-12 | Settle on time → swap | 5 | ✅ |
| REQ-13 | Seller defaults → collateral compensation | 2 | ✅ |
| REQ-14 | OTC approved list ($300K min liquidity) | 2 | ✅ |
| REQ-15 | Unlisted token → warning + verify mint | 4 | ✅ |
| REQ-16 | OTC zero slippage | 2 | ✅ |
| REQ-17 | OTC Seller creates offer | 3 | ✅ |
| REQ-18 | OTC Seller deposits tokens | 2 | ✅ |
| REQ-19 | Buyer browses OTC / private link | 9 | ✅ |
| REQ-20 | OTC accept → auto-swap | 5 | ✅ |
| REQ-21 | createOffer() → offerId | 7 | ✅ |
| REQ-22 | acceptOffer() → lock payment | 2 | ✅ |
| REQ-23 | closeOffer() → refund - 0.5% fee | 4 | ✅ |
| REQ-24 | settleOffer() → token swap | 4 | ✅ |
| REQ-25 | cancelOffer() → refund + compensation | 5 | ✅ |
| REQ-26 | claimCollateral() → buyer claims | 3 | ✅ |
| REQ-27 | Events emitted for all state changes | 3 | ✅ |
| REQ-28 | Multi-chain: Solana + EVM | 9 | ✅ |
| REQ-29 | $WHALES fee discount | 0 | ❌ |

> ✅ Covered: **28**/29 (97%) · ❌ Uncovered: **1** (3%) — REQ-29 needs spec clarification

### Detail — Scenario Mapping

<details>
<summary>Click to expand full REQ → Scenario mapping</summary>

| REQ | Scenarios |
|---|---|
| REQ-1 | SC-1, SC-8, SC-9, SC-13, SC-26, SC-28, SC-31 |
| REQ-2 | SC-2, SC-11, SC-30 |
| REQ-3 | SC-85, SC-97–SC-101, SC-104, SC-107–SC-111 |
| REQ-4 | SC-5, SC-16, SC-18, SC-20, SC-33, SC-80 |
| REQ-5 | SC-6 |
| REQ-6 | SC-3, SC-12, SC-14, SC-15, SC-24, SC-27, SC-29 |
| REQ-7 | SC-3, SC-19, SC-79 |
| REQ-8 | SC-4, SC-25 |
| REQ-9 | SC-6, SC-17 |
| REQ-10 | SC-6 |
| REQ-11 | SC-7, SC-10, SC-35, SC-82 |
| REQ-12 | SC-6, SC-21, SC-22, SC-32, SC-34 |
| REQ-13 | SC-17, SC-23 |
| REQ-14 | SC-45, SC-55 |
| REQ-15 | SC-41, SC-42, SC-45, SC-51 |
| REQ-16 | SC-40, SC-57 |
| REQ-17 | SC-36, SC-47, SC-55 |
| REQ-18 | SC-36, SC-58 |
| REQ-19 | SC-37, SC-38, SC-43, SC-44, SC-49, SC-52–SC-56 |
| REQ-20 | SC-37, SC-39, SC-46, SC-50, SC-59 |
| REQ-21 | SC-61, SC-67, SC-81, SC-86, SC-88, SC-90, SC-91 |
| REQ-22 | SC-62, SC-68 |
| REQ-23 | SC-63, SC-69, SC-73, SC-93 |
| REQ-24 | SC-64, SC-70, SC-74, SC-78 |
| REQ-25 | SC-65, SC-71, SC-75, SC-76, SC-78 |
| REQ-26 | SC-66, SC-72, SC-77 |
| REQ-27 | SC-59, SC-88, SC-89 |
| REQ-28 | SC-60, SC-83, SC-94–SC-96, SC-98, SC-102, SC-103, SC-105 |
| REQ-29 | — |

</details>

> **REQ-29 ($WHALES fee discount):** Spec mentions fee discount utility but does not specify: discount %, criteria (hold vs stake), scope (Pre-Market / OTC / both). Cannot write test cases — needs spec supplement.

---

## 🛡️ Security Review Report

> Auto-generated based on spec analysis. Not a penetration test — a structured review of security risks identified from the spec.

### Attack Surface Summary

| Surface | Risk Level | Details |
|---|---|---|
| **Smart Contract (Escrow)** | 🔴 High | 6 state-changing functions handling user funds — reentrancy, access control, state manipulation risks |
| **Wallet Connection** | 🟡 Medium | Multi-chain wallet integration (Solana + EVM) — session hijack, phishing risks |
| **API Gateway** | 🟡 Medium | Backend services: order matching, price oracle, notifications — injection, rate limiting |
| **Admin Functions** | 🔴 High | Admin triggers settlement — single point of failure, privilege escalation |
| **Token Verification** | 🟡 Medium | Mint address verification for unlisted tokens — fake token, phishing risks |
| **User Input Forms** | 🟡 Medium | Create Offer form (price, amount, token) — injection, overflow |
| **Cross-chain Bridge** | 🔴 High | Solana ↔ EVM communication — replay attacks, attestation manipulation |
| **File Uploads** | ⚪ N/A | No file upload in spec |

### Identified Vulnerabilities

| # | Vulnerability | Severity | Feature |
|---|---|:---:|---|
| V-1 | Reentrancy on settle/cancel/claim | 🔴 Critical | Smart Contract |
| V-2 | Repeated cancel/claim drain | 🔴 Critical | Smart Contract |
| V-3 | Cancel + Settle race condition | 🔴 Critical | Smart Contract |
| V-4 | Admin privilege escalation | 🔴 Critical | Settlement |
| V-5 | Front-running / MEV on acceptOffer | 🟡 Medium | Pre-Market |
| V-6 | Cross-chain signature replay | 🔴 Critical | Multi-chain |
| V-7 | Integer overflow on amount × price | 🟡 Medium | Smart Contract |
| V-8 | XSS in token name/symbol display | 🟡 Medium | OTC Market |
| V-9 | IDOR — sequential offer ID | 🟡 Medium | API |
| V-10 | Flash loan collateral manipulation | 🔴 Critical | Pre-Market |

> 🔴 Critical: **6** · 🟡 Medium: **4**

<details>
<summary>Click to expand OWASP mapping & fix recommendations</summary>

| # | OWASP | Recommendation |
|---|---|---|
| V-1 | A01:2021 Broken Access Control | Implement ReentrancyGuard (OpenZeppelin) on all state-changing functions. Use checks-effects-interactions pattern |
| V-2 | A01:2021 Broken Access Control | Use mutex lock + update state BEFORE external call. Add boolean flag "claimed"/"cancelled" check at function entry |
| V-3 | A04:2021 Insecure Design | Implement mutex lock on offer state. Both settle and cancel must check-and-set offer.status atomically |
| V-4 | A01:2021 Broken Access Control | Add multi-sig requirement for admin actions. Implement timelock (24h delay) for critical admin functions |
| V-5 | A04:2021 Insecure Design | Implement commit-reveal scheme or integrate private mempool (Flashbots/Jito) |
| V-6 | A02:2021 Cryptographic Failures | Include chainId in signed data (EIP-712 for EVM). Verify chain-specific nonce on each chain |
| V-7 | A03:2021 Injection | Use Solidity ≥0.8.0 (built-in overflow protection) or SafeMath. Validate input ranges |
| V-8 | A03:2021 Injection | Escape HTML entities before rendering token metadata. Use content security policy |
| V-9 | A01:2021 Broken Access Control | Use UUID instead of sequential ID. Verify ownership before returning sensitive data |
| V-10 | A04:2021 Insecure Design | Lock collateral permanently (no withdrawal in same block). Verify collateral source |

</details>

### DeFi-Specific Security Findings

| # | Vulnerability | Severity | Attack Vector |
|---|---|:---:|---|
| DV-1 | Reentrancy on 3 fund-moving functions | 🔴 Critical | External call before state update |
| DV-2 | Repeated cancel drain | 🔴 Critical | cancelOffer called multiple times in same block |
| DV-3 | Cancel + Settle race | 🔴 Critical | Submit both simultaneously |
| DV-4 | Flash loan collateral | 🔴 Critical | Borrow → deposit → withdraw in 1 tx |
| DV-5 | MEV front-running on acceptOffer | 🟡 Medium | Mempool observation |
| DV-6 | Admin rugpull potential | 🔴 Critical | Admin can pause + drain |
| DV-7 | Cross-chain replay | 🔴 Critical | Replay tx on another chain |
| DV-8 | Oracle manipulation | 🟡 Medium | Stale/manipulated price feed |

> 🔴 Critical: **6** · 🟡 Medium: **2**

<details>
<summary>Click to expand fix recommendations</summary>

| # | Recommendation |
|---|---|
| DV-1 | ReentrancyGuard + checks-effects-interactions on settleOffer, cancelOffer, claimCollateral |
| DV-2 | Boolean flag `offer.cancelled = true` check at function entry, set before external call |
| DV-3 | Mutex lock on offer status — state transition only valid: Open→Accepted→Settled/Cancelled/Defaulted |
| DV-4 | Prevent collateral withdrawal in the same block as deposit |
| DV-5 | Commit-reveal or private mempool integration |
| DV-6 | Multi-sig + timelock + cap fee changes + no admin withdrawal |
| DV-7 | Chain-specific nonce + EIP-712 domain separator |
| DV-8 | Multi-source oracle + staleness check + TWAP |

</details>

### Recommendations for Dev Team

**🔴 Fix before launch:**

| # | Ref | Action |
|:---:|---|---|
| 1 | V-1, DV-1 | Add `ReentrancyGuard` to all 6 functions. Checks-effects-interactions: update state → emit → transfer |
| 2 | V-2, DV-2 | Boolean `claimed`/`cancelled` flag. Check at entry, set BEFORE external call |
| 3 | V-3, DV-3 | Strict state machine: `Open → Accepted → {Settled ∣ Cancelled ∣ Defaulted}`. Enum for status |
| 4 | V-4, DV-6 | Multi-sig (3/5) for admin functions. 48h timelock for fee change, pause, withdrawal |
| 5 | V-6, DV-7 | Include `chainId` in signed data. EVM: EIP-712 domain separator. Solana: cluster ID |
| 6 | V-10, DV-4 | Prevent collateral withdrawal in same block as deposit. Min lock: 1 block |

**🟡 Fix in next sprint:**

| # | Ref | Action |
|:---:|---|---|
| 7 | V-5, DV-5 | Commit-reveal for acceptOffer or integrate Jito (Solana) / Flashbots (EVM) |
| 8 | V-7 | Validate input ranges: amount > 0, price > 0, collateral > min. SafeMath for all math |
| 9 | V-8 | Strict HTML escaping for on-chain metadata. Add Content-Security-Policy header |
| 10 | V-9 | Migrate sequential IDs → UUID. Add ownership check on API layer |

**ℹ️ Security assumptions (not tested by this suite):**
- Smart contract audit has been completed (spec section 5.1 states "Full audit completed") — this test suite does not replace formal verification
- HTTPS enforcement is handled at the load balancer / CDN layer
- Database encryption at rest is handled by the infra team
- DDoS protection is handled by CDN/WAF
- Solana program security (BPF-specific vulnerabilities) is out of scope — requires a separate audit

---

## 🚨 Risk Areas & Notes

- `AMBIGUOUS:` REQ-29 — $WHALES fee discount mechanism is unclear: what discount percentage, based on what criteria (hold amount, stake amount, tier), applied to Pre-Market or OTC or both? Cannot write test cases until the spec is supplemented
- `AMBIGUOUS:` Cross-chain OTC (SC-60) — Spec mentions multi-chain support but does not describe the cross-chain swap mechanism (Solana seller ↔ EVM buyer). Needs clarification: bridge integration or same-chain only?
- `AMBIGUOUS:` Settlement countdown trigger — Spec says "admin triggers settle" but does not clarify: single admin or multi-sig? Automated detection or manual? Rollback if TGE is delayed?
- `AMBIGUOUS:` Collateral ratio — Spec does not specify minimum collateral ratio. Can the seller set extremely low collateral? A clear business rule is needed
- `MISSING:` API endpoint paths — Spec does not define API contracts. Test cases SC-28, SC-29, SC-30, SC-54, SC-55, SC-56 use inferred paths — additional API spec needed
- `MISSING:` Error messages — Spec does not define exact error messages for validation failures. Scenarios marked `[TBD]` need to be updated when the UI spec is available
- `MISSING:` Rate limiting configuration — Spec does not specify rate limits for the API. Recommended: 100 req/min for read, 10 req/min for write operations
- `MISSING:` Gas limit configurations — Max gas per contract function is not specified
- `HIGH RISK:` Admin privilege — Admin can trigger settlement for any project. If the admin key is compromised → can trigger fake TGE settlement. Recommend multi-sig + timelock
- `HIGH RISK:` Repeated cancel/claim attack vector — This is the most common attack vector for escrow contracts. Requires reentrancy guard + state check at the beginning of each function
- `HIGH RISK:` Front-running risk — acceptOffer on the public mempool is easily front-run by MEV bots. Impact: good offers get sniped before retail users
- `TEST DATA:` Testnet deployment needed (Solana Devnet + Goerli/Sepolia) with fake tokens to test the full flow
- `TEST DATA:` Test wallets needed: at least 3 (seller, buyer, attacker) on each chain
- `TEST DATA:` Mock TGE event needed — contract that allows admin to trigger settlement on testnet
