# Test Case Generator Skill

> **v2.1** · Claude Code Skill · Gherkin/BDD · Any Spec, Any Domain

A Claude Code skill that automatically generates exhaustive, production-quality test cases in **Gherkin/BDD format** from any product specification — regardless of format (PRD, API contract, user stories, compliance doc) or domain (web, mobile, API, CLI, IoT, ML/AI, desktop, game, DeFi/Web3).

---

## What It Does

Paste or upload a spec (feature doc, PRD, API contract, user story) and this skill will:

1. **Parse & analyze** the spec — extracting features, user roles, core flows, data inputs, business rules, and integrations
2. **Extract requirements** — number every testable requirement → Requirement Inventory
3. **Generate test scenarios** grouped by feature, covering every test category, with `# Covers: REQ-X` traceability
4. **Output structured Gherkin** with tags, `Scenario Outline` tables, `Background` blocks, and specific assertions
5. **Produce a Coverage Matrix** + **Requirement Traceability Matrix** — trace every test back to its spec requirement
6. **Generate a Security Review Report** — attack surfaces, vulnerabilities, actionable recommendations for dev team
7. **Flag risk areas** — ambiguous requirements, high-risk flows, and recommended test data setup

---

## How to Use

```
1. Open Claude Code in your terminal
2. Paste or upload your spec (spec.md, PRD, or requirements doc)
3. Say: "write test cases for this spec"
   → Or say "lite mode" / "quick coverage" for simple specs (≤3 requirements → only happy-path, negative, security)
4. If spec is too large (>10K words) → skill stops and proposes a split plan
5. Review the generated test suite, traceability matrix, and security report
6. Iterate: approve, edit, or reject individual scenarios
```

---

## Trigger Phrases

This skill activates when you:

- Upload or paste a spec (`.md`, PRD, API contract, user stories, bullet-point requirements, or any doc) and ask for test cases
- Say things like *"write tests for this spec"*, *"generate QA cases"*, *"cover this feature with tests"*, *"what should I test?"*, *"create test scenarios from this doc"*
- Paste a spec and ask for testing help — works for web apps, mobile apps, APIs, CLI tools, IoT, ML/AI, desktop, games, DeFi protocols, or any software product

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

> Prompt Version: v2.1.0
> Generated from: [spec file]
> Total Scenarios: [count]
> Coverage: [categories]

## 📑 Requirement Inventory          → every testable requirement numbered [REQ-1]...[REQ-N]

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

  Each scenario includes: # Covers: REQ-X traceability comment

## 🗺️ Coverage Matrix               → scenarios per feature × category
## 🔗 Requirement Traceability Matrix → every REQ mapped to scenarios (✅ Covered / ❌ Uncovered)
## 🛡️ Security Review Report         → attack surfaces, vulnerabilities, OWASP mapping, actionable fixes
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
> — Requirement Inventory, Traceability Matrix, Coverage Matrix, Security Review Report, and Risk Areas included.

---

## Quality Rules

- Scenario names are **specific and human-readable** — not *"Test login"* but *"User with valid credentials successfully logs in and is redirected to dashboard"*
- `Scenario Outline` + `Examples` tables for parameterized cases
- Every `When` has a meaningful, specific `Then`
- No vague assertions — exact error messages, field names, status codes
- `Background` blocks for shared preconditions
- `# Note:` comments flag scenarios needing special test data or environment setup
