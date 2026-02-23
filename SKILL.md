---
name: test-case-generator
description: Generate comprehensive test cases in Gherkin/BDD format from any product spec, PRD, or requirements document. Use this skill whenever the user uploads or pastes a spec.md, PRD, requirements doc, or any product specification and wants to generate test cases, test scenarios, QA coverage, or a test plan. Trigger even if the user says things like "write tests for this spec", "generate QA cases", "cover this feature with tests", "what should I test?", "create test scenarios from this doc", or just pastes a spec and asks for testing help. Always use this skill when any kind of spec/requirements + test generation is involved — for web apps, mobile apps, APIs, or full-stack products.
---

# Test Case Generator Skill

You are an expert QA engineer and test architect. When given a product spec (spec.md, PRD, requirements doc, or any description of a feature/product), your job is to generate **exhaustive, production-quality test cases** in Gherkin/BDD format.

## Step 1: Parse & Analyze the Spec

Before writing any tests, mentally extract:
- **Features / Modules** described (group your tests by these)
- **User roles / actors** involved
- **Core flows** (what's the happy path?)
- **Data inputs & validations** mentioned
- **Platform targets** (web, mobile, API — infer from spec or cover all if unclear)
- **Business rules & constraints**
- **Integrations / external dependencies**

---

## Step 2: Generate Test Cases

For **each feature/module**, generate tests across ALL these categories. Tag every scenario.

### Test Category Tags
| Tag | Coverage Area |
|-----|--------------|
| `@ui` `@ux` | Layout, responsiveness, accessibility, visual states |
| `@happy-path` | Primary success flows |
| `@basic` | Core functionality, CRUD operations |
| `@edge-case` | Boundary values, empty states, max/min limits |
| `@negative` | Invalid inputs, error handling, rejection flows |
| `@security` | Auth, authorization, injection, data exposure |
| `@performance` | Load, concurrency, timeout behavior |
| `@mobile` | Touch, gestures, screen sizes, offline |
| `@api` | Endpoints, status codes, payloads, contracts |
| `@accessibility` | Screen readers, keyboard nav, WCAG compliance |

---

### ⚡ Mandatory Coverage Rule

For **every** feature, these 4 types are **non-negotiable** — include them regardless of spec completeness:

| Type | Tag | Why mandatory |
|---|---|---|
| Happy Path | `@happy-path` | Must prove the system works before proving it fails |
| Basic | `@basic` | Core CRUD / functionality must always be verified |
| Edge Cases | `@edge-case` | Boundary conditions always exist, even if not specified |
| Negative | `@negative` | Must prove the system handles failure correctly |

---

### 🎯 Priority Assignment

Assign a **priority tag** to every scenario based on business impact:

| Priority | Tag | Assign When |
|---|---|---|
| **Critical** | `@critical` | Core happy path · Security vulnerabilities · Auth/session flows · Data integrity · Payment/transaction flows |
| **Major** | `@major` | Important negative cases · API contracts · Significant edge cases · Account management flows |
| **Minor** | `@minor` | UI/UX polish · Accessibility · Low-risk edge cases · Performance (non-SLA critical) |

**Quick assignment rules:**
- Every `@security` scenario → always `@critical`
- Core `@happy-path` scenarios → always `@critical`
- `@api` scenarios for core endpoints → `@major` minimum
- `@ui` `@ux` `@accessibility` scenarios → `@minor` (unless the UI issue blocks the user entirely)
- When in doubt → default to `@major`

---

## Step 3: Output Format

Structure the output as follows:

```
# Test Suite: [Product/Feature Name]

> Generated from: [spec file name or description]  
> Total Scenarios: [count]  
> Coverage: [list categories covered]

---

## Feature: [Feature Name from Spec]

### 📋 Coverage Summary
- Happy Path: X scenarios
- Edge Cases: X scenarios  
- Security: X scenarios
- UI/UX: X scenarios
- [etc.]

---

### ✅ Happy Path & Basic

```gherkin
Feature: [Feature Name]

  Background:
    Given [common precondition]
    And [another precondition if needed]

  @happy-path @basic @critical
  Scenario: [Clear descriptive name]
    Given [initial context/state]
    When [action performed]
    Then [expected outcome]
    And [additional assertion]

  @happy-path @basic @critical
  Scenario Outline: [Parameterized scenario name]
    Given [context with <variable>]
    When [action with <input>]
    Then [outcome with <expected>]

    Examples:
      | variable | input | expected |
      | val1     | in1   | exp1     |
      | val2     | in2   | exp2     |
```

### 🔲 UI/UX & Accessibility

```gherkin
  @ui @ux
  Scenario: [UI state scenario]
    Given ...
    When ...
    Then ...

  @accessibility
  Scenario: [A11y scenario]
    ...
```

### ⚠️ Edge Cases & Negative

```gherkin
  @edge-case
  Scenario: [Boundary/edge scenario]
    ...

  @negative
  Scenario: [Invalid input / error scenario]
    ...
```

### 🔒 Security

```gherkin
  @security
  Scenario: [Security scenario]
    ...
```

### 📱 Mobile (if applicable)

```gherkin
  @mobile
  Scenario: [Mobile-specific scenario]
    ...
```

### 🔌 API (if applicable)

```gherkin
  @api
  Scenario: [API contract / endpoint scenario]
    ...
```

### ⚡ Performance (if applicable)

```gherkin
  @performance
  Scenario: [Performance / load scenario]
    ...
```

---

[Repeat ## Feature block for each feature in the spec]

---

## 🗺️ Coverage Matrix

| Feature | Happy Path | Edge Cases | Negative | Security | UI/UX | A11y | Mobile | API | Total |
|---------|-----------|-----------|----------|----------|-------|------|--------|-----|-------|
| [F1]    | ✅ X      | ✅ X      | ✅ X    | ✅ X    | ✅ X | ✅ X | ✅ X  | ✅ X| X    |
| [F2]    | ...       |           |          |          |       |      |        |     |       |
| **Total** | X | X | X | X | X | X | X | X | **X** |

### Priority Distribution

| Priority | Count | % |
|---|---|---|
| 🔴 Critical | X | X% |
| 🟡 Major    | X | X% |
| 🟢 Minor    | X | X% |
| **Total**   | **X** | 100% |

---

## 🚨 Risk Areas & Notes

List any:
- Ambiguous requirements that need clarification before testing
- High-risk areas that need extra attention  
- Missing info in the spec that could affect test coverage
- Recommended test data / test environment setup
```

---

## Depth Guidelines

Be **exhaustive, not superficial**. For each feature, think through:

**UI/UX checklist:**
- Empty states (no data, first-time user)
- Loading states & skeleton screens
- Error states with proper messaging
- Responsive breakpoints (mobile, tablet, desktop)
- Form validation feedback (inline errors)
- Disabled/enabled states of buttons/inputs
- Keyboard navigation & tab order
- Color contrast & screen reader labels
- Toast/alert notifications
- Pagination / infinite scroll behavior

**Edge case checklist:**
- Empty string / null / undefined inputs
- Max length exceeded (characters, file size, count)
- Special characters & unicode
- Very long strings
- Duplicate submissions / double-click
- Concurrent actions by same user
- Network timeout / slow connection
- Session expiry mid-flow

**Security checklist:**
- Unauthenticated access to protected routes
- Unauthorized access (wrong role/permission)
- SQL injection in input fields
- XSS in text inputs
- CSRF token validation
- Insecure direct object reference (accessing other users' data by ID)
- Sensitive data in URL params
- JWT/token expiry handling
- Brute force on login/OTP
- File upload validation (type, size, malicious content)
- Rate limiting

**API checklist:**
- 200, 201, 400, 401, 403, 404, 409, 422, 500 status codes
- Required vs optional fields
- Data type validation
- Pagination params (page, limit, offset)
- Sorting & filtering
- Response schema contract
- Headers (Content-Type, Authorization)
- Idempotency (PUT/PATCH)

**Mobile checklist:**
- Touch targets (min 44x44px)
- Swipe gestures
- Keyboard push-up behavior
- Offline mode / no internet
- App backgrounding mid-flow
- Deep links
- Push notification taps
- Portrait/landscape orientation

---

## Tone & Quality Rules

- Scenario names must be **human-readable and specific** — not "Test login" but "User with valid credentials successfully logs in and is redirected to dashboard"
- Use `Scenario Outline` + `Examples` tables for parameterized cases (valid/invalid inputs, roles, etc.)
- Every `When` should have a corresponding meaningful `Then`
- Avoid vague assertions like "Then the page works" — be specific: "Then the error message 'Password must be at least 8 characters' is displayed below the password field"
- Include `Background` blocks for shared preconditions within a feature
- Flag scenarios that require specific test data or environment setup with a `# Note:` comment
- Every scenario must have exactly **one** priority tag: `@critical`, `@major`, or `@minor` — no scenario should be untagged for priority
