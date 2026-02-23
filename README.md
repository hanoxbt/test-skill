# Test Case Generator Skill

> **v2.0** · Claude Code Skill · Gherkin/BDD · Web2 + Web3/DeFi

A Claude Code skill that automatically generates exhaustive, production-quality test cases in **Gherkin/BDD format** from any product specification, PRD, or requirements document — supporting **Web2, Web3, and DeFi** products.

---

## What It Does

Paste or upload a spec (feature doc, PRD, API contract, user story) and this skill will:

1. **Parse & analyze** the spec — extracting features, user roles, core flows, data inputs, business rules, and integrations
2. **Generate test scenarios** grouped by feature, covering every test category
3. **Output structured Gherkin** with tags, `Scenario Outline` tables, `Background` blocks, and specific assertions
4. **Produce a Coverage Matrix** showing scenario counts per feature/category
5. **Flag risk areas** — ambiguous requirements, high-risk flows, and recommended test data setup

---

## How to Use

```
1. Open Claude Code in your terminal
2. Paste or upload your spec (spec.md, PRD, or requirements doc)
3. Say: "write test cases for this spec"
4. Review the generated test suite
5. Iterate: approve, edit, or reject individual scenarios
```

---

## Trigger Phrases

This skill activates when you:

- Upload or paste a `spec.md`, PRD, or requirements doc and ask for test cases
- Say things like *"write tests for this spec"*, *"generate QA cases"*, *"cover this feature with tests"*, *"what should I test?"*, *"create test scenarios from this doc"*
- Paste a spec and ask for testing help — for web apps, mobile apps, APIs, DeFi protocols, or full-stack products

---

## Test Categories Covered

**16 categories** — 10 standard + 6 Web3/DeFi (conditional)

| | Tag | What It Tests |
|---|-----|--------------|
| **Core** | `@happy-path` | Primary success flows |
| | `@basic` | Core functionality, CRUD operations |
| | `@edge-case` | Boundary values, empty states, max/min limits |
| | `@negative` | Invalid inputs, error handling, rejection flows |
| **Quality** | `@security` | Auth, authorization, injection, data exposure |
| | `@ui` `@ux` | Layout, responsiveness, visual states, form validation |
| | `@accessibility` | Screen readers, keyboard navigation, WCAG compliance |
| **Platform** | `@mobile` | Touch, gestures, screen sizes, offline behavior |
| | `@api` | Endpoints, status codes, payloads, response contracts |
| | `@performance` | Load, concurrency, timeout behavior |
| **Web3/DeFi** | `@web3` | Blockchain interactions, dApp behavior, chain states |
| | `@wallet` | Wallet connection, signing, network switching, sessions |
| | `@defi-security` | DeFi exploit vectors: reentrancy, flash loan, oracle, MEV |
| | `@smart-contract` | Contract calls, gas, nonce, state verification, events |
| | `@token` | Token transfers, approvals, decimals, balances, standards |
| | `@blockchain` | Chain-level: confirmations, reorg, mempool, gas price |

---

## Output Structure

```
# Test Suite: [Product/Feature Name]

> Prompt Version: v2.0.0
> Generated from: [spec file]
> Total Scenarios: [count]
> Coverage: [categories]

## Feature: [Feature Name]

  ### 📋 Coverage Summary
  ### ✅ Happy Path & Basic         → @happy-path @basic
  ### 🔲 UI/UX & Accessibility      → @ui @ux @accessibility
  ### ⚠️ Edge Cases & Negative       → @edge-case @negative
  ### 🔒 Security                    → @security
  ### 📱 Mobile                      → @mobile (if applicable)
  ### 🔌 API                         → @api (if applicable)
  ### ⚡ Performance                  → @performance (if applicable)
  ### 🔗 Web3 / DeFi                 → @web3 @defi-security @wallet @token (if applicable)

## 🗺️ Coverage Matrix               → scenarios per feature × category
## 🚨 Risk Areas & Notes            → ambiguities, high-risk flows, test data needs
```

---

## Depth Checklists

> Each feature is tested against relevant checklists below. Web3/DeFi checklists activate **only** when the spec contains DeFi keywords.

### 🌐 Web2 Checklists

### UI/UX
- Empty states, loading states, error states
- Responsive breakpoints (mobile / tablet / desktop)
- Inline form validation with specific error messages
- Disabled/enabled button states
- Keyboard navigation & tab order
- Toast/alert notifications, pagination behavior

### Edge Cases
- Empty / null / undefined inputs
- Max length exceeded (characters, file size, count)
- Special characters & unicode
- Duplicate submissions / double-click
- Concurrent actions, network timeout, session expiry mid-flow

### Security
- Unauthenticated & unauthorized access
- SQL injection, XSS, CSRF token validation
- Insecure direct object reference
- JWT/token expiry, brute force on login/OTP
- File upload validation, rate limiting

### API
- Status codes: 200, 201, 400, 401, 403, 404, 409, 422, 500
- Required vs optional fields, data type validation
- Pagination, sorting & filtering
- Response schema contract, idempotency (PUT/PATCH)

### Mobile
- Touch targets (min 44×44px), swipe gestures
- Keyboard push-up behavior, offline mode
- App backgrounding mid-flow, deep links
- Portrait/landscape orientation

### 🔗 Web3/DeFi Checklists (conditional — activated by DeFi keywords)

### DeFi Security
- Reentrancy, flash loan, oracle manipulation attacks
- Front-running / MEV, sandwich attacks
- Token approval exploits (infinite approval), slippage manipulation
- Governance attacks, bridge vulnerabilities, signature replay
- Rugpull indicators, access control bypass, private key exposure

### Wallet Integration
- MetaMask / WalletConnect connection, wrong network, chain switching
- Transaction signing flow, gas estimation, tx states (pending/confirmed/failed)
- Wallet disconnect, reconnect, multiple accounts, hardware wallet

### Token / Fund Management
- ERC-20/721/1155 transfers, approvals, revoke, allowance checks
- Balance display with correct decimals (USDC=6, ETH=18), dust handling
- Cross-chain bridge transfers, max balance operations

### Smart Contract & Blockchain
- Contract call success/failure, gas estimation, revert with reason
- Nonce management, event emission, multicall/batch transactions
- Blockchain errors: TRANSACTION_REVERTED, OUT_OF_GAS, USER_REJECTED, INSUFFICIENT_FUNDS

### Financial Precision
- Wei/Gwei/ETH conversion, token decimal handling, rounding behavior
- Price display (very small/large numbers), APY/APR calculation accuracy
- Fee breakdown, slippage calculation, LP share percentage

---

## Sample Output

> See [`test-cases-sample-output.md`](./test-cases-sample-output.md) for a full real-world example:
> **79 scenarios** across 3 features (Registration, Login, Forgot Password)
> — Coverage Matrix, Priority Distribution, and Risk Areas included.

---

## Quality Rules

- Scenario names are **specific and human-readable** — not *"Test login"* but *"User with valid credentials successfully logs in and is redirected to dashboard"*
- `Scenario Outline` + `Examples` tables for parameterized cases
- Every `When` has a meaningful, specific `Then`
- No vague assertions — exact error messages, field names, status codes
- `Background` blocks for shared preconditions
- `# Note:` comments flag scenarios needing special test data or environment setup
