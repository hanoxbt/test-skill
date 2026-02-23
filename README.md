# Test Case Generator Skill

A Claude Code skill that automatically generates exhaustive, production-quality test cases in **Gherkin/BDD format** from any product specification, PRD, or requirements document.

---

## What It Does

Paste or upload a spec (feature doc, PRD, API contract, user story) and this skill will:

1. **Parse & analyze** the spec — extracting features, user roles, core flows, data inputs, business rules, and integrations
2. **Generate test scenarios** grouped by feature, covering every test category
3. **Output structured Gherkin** with tags, `Scenario Outline` tables, `Background` blocks, and specific assertions
4. **Produce a Coverage Matrix** showing scenario counts per feature/category
5. **Flag risk areas** — ambiguous requirements, high-risk flows, and recommended test data setup

---

## Trigger Phrases

This skill activates when you:

- Upload or paste a `spec.md`, PRD, or requirements doc and ask for test cases
- Say things like *"write tests for this spec"*, *"generate QA cases"*, *"cover this feature with tests"*, *"what should I test?"*, *"create test scenarios from this doc"*
- Paste a spec and ask for testing help — for web apps, mobile apps, APIs, or full-stack products

---

## Test Categories Covered

| Tag | What It Tests |
|-----|--------------|
| `@happy-path` | Primary success flows |
| `@basic` | Core functionality, CRUD operations |
| `@edge-case` | Boundary values, empty states, max/min limits |
| `@negative` | Invalid inputs, error handling, rejection flows |
| `@security` | Auth, authorization, injection, data exposure |
| `@ui` `@ux` | Layout, responsiveness, visual states, form validation |
| `@accessibility` | Screen readers, keyboard navigation, WCAG compliance |
| `@mobile` | Touch, gestures, screen sizes, offline behavior |
| `@api` | Endpoints, status codes, payloads, response contracts |
| `@performance` | Load, concurrency, timeout behavior |

---

## Output Structure

```
# Test Suite: [Product/Feature Name]

> Generated from: [spec file]
> Total Scenarios: [count]
> Coverage: [categories]

## Feature: [Feature Name]

### Coverage Summary
### Happy Path & Basic        → @happy-path @basic
### UI/UX & Accessibility     → @ui @ux @accessibility
### Edge Cases & Negative     → @edge-case @negative
### Security                  → @security
### Mobile                    → @mobile (if applicable)
### API                       → @api (if applicable)
### Performance               → @performance (if applicable)

## Coverage Matrix            → scenarios per feature × category
## Risk Areas & Notes         → ambiguities, high-risk flows, test data needs
```

---

## Depth Checklist

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

---

## Sample Output

See [`test-cases-sample-output.md`](./test-cases-sample-output.md) for a full real-world example — **79 scenarios** generated for a User Authentication module (Registration, Login, Forgot Password), covering all categories with a complete Coverage Matrix and Risk Areas section.

---

## Quality Rules

- Scenario names are **specific and human-readable** — not *"Test login"* but *"User with valid credentials successfully logs in and is redirected to dashboard"*
- `Scenario Outline` + `Examples` tables for parameterized cases
- Every `When` has a meaningful, specific `Then`
- No vague assertions — exact error messages, field names, status codes
- `Background` blocks for shared preconditions
- `# Note:` comments flag scenarios needing special test data or environment setup
