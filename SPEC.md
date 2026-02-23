# Spec: Test Case Generator Skill

**Version:** 1.0
**Author:** QA Team
**Last Updated:** 2026-02-23
**Skill File:** `test-case-generator/SKILL.md`

---

## 1. Objective

This skill enables AI to automatically generate exhaustive, production-quality test cases from any product specification document (spec, PRD, requirements doc). Output follows the Gherkin/BDD standard, grouped by feature and tagged by test type.

**Problems this skill solves:**
- QA engineers spend many hours manually reading specs and writing test cases
- Edge cases, security scenarios, and mobile-specific behaviors are easily missed
- No coverage matrix to track test coverage

**Expected outcome:**
Input a spec → instantly receive a complete test case suite, ready to use or import into a test management tool.

---

## 2. Input

### 2.1 Accepted Formats

| Input type | Examples |
|---|---|
| File upload | `spec.md`, `prd.md`, `requirements.txt`, `feature.md` |
| Pasted text | User copy-pastes spec content into chat |
| Verbal description | User summarizes the feature in natural language |
| Word/PDF file | `.docx`, `.pdf` containing spec content |

### 2.2 Minimum Required Spec Content

The skill works best when the spec includes at least:

- **Feature name** — name of the feature/module
- **User flow** — description of what the user does
- **Input fields** — data fields and validation rules
- **Expected behavior** — the desired outcome

The skill can still run with an incomplete spec, but will **flag ambiguous sections** in the Risk Notes at the end of the output.

### 2.3 Trigger Phrases (when the skill is invoked)

The skill activates when the user says things like:
- _"Write test cases for this spec"_
- _"Generate test cases / QA cases"_
- _"Cover this feature with tests"_
- _"I need a test plan for feature X"_
- _"What should I test?"_
- _"Create test scenarios from this doc"_
- User pastes a spec without saying anything → AI proactively asks if they want test cases generated

---

## 3. Processing Steps (Step-by-Step)

### STEP 1 — Read and Analyze the Spec

> **Goal:** Fully understand the spec before writing any test cases.

The AI must extract the following information from the spec:

```
✅ List of Features / Modules (used for grouping tests)
✅ User roles / actors (admin, user, guest, etc.)
✅ Main happy path for each feature
✅ Input fields and validation rules
✅ Business rules and constraints
✅ Platform targets (web / mobile / API)
✅ External dependencies / integrations
✅ Non-functional requirements (performance, security, accessibility)
```

**If the spec is ambiguous or incomplete:**
Do not stop to ask. Continue generating tests, then flag the missing parts in the **Risk Areas & Notes** section at the end of the output.

---

### STEP 2 — Plan Coverage

> **Goal:** Determine which test types to generate for each feature, based on the spec content.

For each feature, the AI selects the relevant test types from the list below:

| Tag | Test Type | When to Include |
|-----|-----------|-----------------|
| `@happy-path` | Primary success flow | Always |
| `@basic` | Core CRUD operations | Always |
| `@edge-case` | Boundary values, empty states | Always |
| `@negative` | Invalid inputs, rejection flows | Always |
| `@security` | Auth, injection, data exposure | When there is login/data/forms |
| `@ui` `@ux` | Layout, visual states, responsiveness | When spec mentions UI |
| `@accessibility` | WCAG, screen reader, keyboard | When spec mentions a11y or public-facing web |
| `@mobile` | Touch, gesture, offline, orientation | When platform is mobile/app |
| `@api` | Endpoint, status codes, payload | When there is an API/backend |
| `@performance` | Load, concurrency, timeout | When spec mentions a performance SLA |

**Important rule:** Never omit `@happy-path`, `@basic`, `@edge-case`, or `@negative` for any feature. These 4 types are mandatory.

---

### STEP 3 — Write Test Cases in Gherkin

> **Goal:** Write each test case in Gherkin/BDD format — complete and unambiguous.

#### Standard Gherkin Structure

```gherkin
Feature: [Feature Name]

  Background:                          ← Shared preconditions
    Given [initial state]
    And [additional condition]

  @tag1 @tag2
  Scenario: [Specific descriptive name]
    Given [context / state]
    When [action the user performs]
    Then [expected outcome]
    And [additional assertion if needed]

  @tag1 @tag2
  Scenario Outline: [Name for parameterized case]
    Given [context with <variable>]
    When [action with <input>]
    Then [result with <expected>]

    Examples:
      | variable | input | expected |
      | val1     | in1   | exp1     |
      | val2     | in2   | exp2     |
```

#### Quality Checklist per Scenario

Before finishing a scenario, the AI self-checks:

- [ ] Scenario name is specific enough to understand without reading the body (not "Test login" but "User with valid credentials logs in successfully and is redirected to dashboard")
- [ ] `Then` must be specific — not "Then it works" but clearly state which element, what message, what behavior
- [ ] Use `Scenario Outline` when there are 2+ similar input sets
- [ ] Use `Background` for repeated preconditions within the same feature
- [ ] Attach the correct `@tag` to each scenario
- [ ] Add a `# Note:` comment if the scenario requires special test data or a specific environment setup

---

### STEP 4 — Apply Checklists by Test Type

> **Goal:** Ensure no important cases are missed by cross-referencing checklists.

#### UI/UX Checklist
- Empty state (no data, first-time user)
- Loading state / skeleton screen
- Error state with specific messages
- Responsive: mobile (375px), tablet (768px), desktop (1280px+)
- Form validation: inline errors, when shown, where shown
- Button disabled/enabled states
- Keyboard navigation & tab order
- Color contrast & screen reader labels
- Toast/notification/alert behavior
- Pagination / infinite scroll

#### Edge Case Checklist
- Empty string, null, undefined input
- Max length exceeded (characters, file size, item count)
- Special characters & unicode (accented letters, emoji, special symbols)
- Very long strings
- Double-submit / rapid clicking
- Concurrent actions (multiple open tabs)
- Network timeout / connection lost mid-flow
- Session expiry during an active operation

#### Security Checklist
- Accessing auth-required routes without a token
- Accessing another user's resource (IDOR)
- SQL injection in input fields
- XSS in text inputs
- CSRF token validation
- Sensitive data in URL params
- JWT/token expiry handling
- Brute force on login/OTP
- File upload: type, size, malicious content validation
- API rate limiting

#### API Checklist
- HTTP status codes: 200, 201, 400, 401, 403, 404, 409, 422, 500
- Required vs optional fields
- Data type validation
- Pagination (page, limit, offset)
- Sorting & filtering params
- Response schema contract
- Headers: Content-Type, Authorization
- Idempotency for PUT/PATCH

#### Mobile Checklist
- Touch targets minimum 44×44px
- Swipe gestures
- Keyboard does not obscure inputs/buttons
- Offline mode
- App backgrounded mid-flow
- Deep links
- Push notification tap
- Portrait/Landscape orientation

#### Performance Checklist
- Response time within SLA (if spec mentions it)
- Behavior under high load (concurrent users)
- Timeout handling
- Retry logic

---

### STEP 5 — Format and Export Output

> **Goal:** Organize all test cases into a clear, readable, easily importable document.

#### Complete Output Structure

```
# Test Suite: [Product / Feature Name]

> Generated from: [spec file name or description]
> Total Scenarios: [total count]
> Coverage: [list of categories covered]

---

## Feature: [Feature Name 1]

### 📋 Coverage Summary
- Happy Path: X scenarios
- Edge Cases: X scenarios
- Security: X scenarios
- UI/UX: X scenarios
[etc.]

---

### ✅ Happy Path & Basic
[Gherkin scenarios]

### 🔲 UI/UX & Accessibility
[Gherkin scenarios]

### ⚠️ Edge Cases & Negative
[Gherkin scenarios]

### 🔒 Security
[Gherkin scenarios]

### 📱 Mobile       ← Only included when relevant
[Gherkin scenarios]

### 🔌 API          ← Only included when relevant
[Gherkin scenarios]

### ⚡ Performance  ← Only included when relevant
[Gherkin scenarios]

---

[Repeat ## Feature block for each feature]

---

## 🗺️ Coverage Matrix

| Feature | Happy Path | Edge Cases | Negative | Security | UI/UX | Mobile | API | Total |
|---------|-----------|-----------|----------|----------|-------|--------|-----|-------|
| F1      | ✅ X      | ✅ X      | ✅ X    | ✅ X    | ✅ X | ✅ X  | ✅ X| X    |
| Total   | X         | X         | X        | X        | X     | X      | X   | X    |

---

## 🚨 Risk Areas & Notes

- [Ambiguity 1]: Requirement needs clarification before testing
- [Risk Area 1]: Area requiring extra attention
- [Missing Info]: Information missing from spec that affects coverage
- [Test Data]: Suggested data to prepare for the test environment
```

---

## 4. Output

### 4.1 Output File

Output is saved as a `.md` file named: `test-suite-[feature-name].md`

### 4.2 Output Quality Criteria

| Criterion | Description |
|-----------|-------------|
| **Completeness** | Each feature has all 4 mandatory types: happy path, basic, edge case, negative |
| **Specificity** | No vague scenarios — Then always specifies the exact element, message, or behavior |
| **Gherkin validity** | Valid syntax, importable into Cucumber/Behave/Playwright |
| **Traceability** | Each scenario has correct tags, filterable by test type |
| **Risk awareness** | Risk Notes section clearly states ambiguities and missing info from the source spec |
| **Coverage visibility** | Coverage Matrix provides a quick overview of coverage at the end of the document |

### 4.3 Good vs Bad Output Examples

**❌ Bad — too generic:**
```gherkin
Scenario: Test login
  Given user is on login page
  When user logs in
  Then user is logged in
```

**✅ Good — specific and clear:**
```gherkin
@happy-path @basic
Scenario: User with valid credentials logs in successfully
  Given a user is registered with email "user@example.com" and password "SecurePass1!"
  And the email has been verified
  When the user enters email "user@example.com"
  And the user enters password "SecurePass1!"
  And the user clicks the "Login" button
  Then the user is redirected to "/dashboard"
  And the user's name is displayed in the navigation bar
  And the session cookie is set correctly

@negative
Scenario: Login fails after 5 consecutive incorrect password attempts — account is locked
  Given a user exists with email "user@example.com"
  When the user enters the wrong password 5 times in a row
  Then the account is locked
  And the message "Your account has been temporarily locked for 15 minutes due to too many failed attempts" is displayed
  And the user cannot log in even with the correct password
  # Note: Test with concurrent requests to verify lockout cannot be bypassed
```

---

## 5. Constraints & Limitations

- **Do not fabricate business logic:** If the spec does not mention a timeout value, the AI does not invent a number — write `[TBD]` and flag it in Risk Notes
- **Never skip security:** Even if the spec does not mention security, the AI must always generate at least 3–5 security scenarios for any feature with form inputs or authentication
- **Mobile only when relevant:** If the spec is a pure API with no UI, skip `@mobile` and `@ui` scenarios
- **Do not write executable code:** Output is Gherkin spec only, not automation code (Cypress, Playwright, etc.) — unless the user explicitly requests it
- **Number of scenarios:** No upper limit — exhaustiveness is the priority. An average spec (3–5 features) typically produces 50–100+ scenarios

---

## 6. Edge Cases of the Skill Itself

| Situation | How to Handle |
|---|---|
| Spec is too short (< 10 lines) | Generate tests based on what is available, flag missing information in Risk Notes |
| Spec has many features (> 10) | Divide output into clear sections, with a summary Coverage Matrix at the end |
| Spec is in a non-English language | Generate test cases in the same language, keep Gherkin keywords in English (Given/When/Then) |
| Spec covers both web and mobile | Cover both platforms, use tags to distinguish |
| User wants only one test type | Generate only that type, but still include Coverage Matrix and Risk Notes |
| Spec contains internal contradictions | Flag the contradiction in Risk Notes, generate tests for both interpretations |

---

## 7. End-to-End Example

**Input:**

> User uploads file `spec-checkout.md` with content:
> "Checkout feature: User selects products, enters a shipping address, chooses a payment method (COD or VNPay), and confirms the order. If VNPay, redirect to the payment gateway and handle the callback."

**AI-generated output:**

```
# Test Suite: Checkout Flow

> Generated from: spec-checkout.md
> Total Scenarios: 47
> Coverage: Happy Path, Basic, Edge Cases, Negative, Security, UI/UX, Mobile, API

## Feature: Product Selection & Shipping Address
[... 12 scenarios ...]

## Feature: COD Payment
[... 8 scenarios ...]

## Feature: VNPay Payment
[... 15 scenarios ...]

## Feature: Order Confirmation
[... 12 scenarios ...]

## 🗺️ Coverage Matrix
[table]

## 🚨 Risk Areas & Notes
- AMBIGUOUS: Spec does not mention timeout for VNPay callback — confirm with dev team
- MISSING: Unclear behavior when user modifies cart after reaching the payment step
- HIGH RISK: VNPay callback must verify signature — need dedicated test case for signature tampering
- TEST DATA: Requires VNPay sandbox credentials and test card numbers
```
