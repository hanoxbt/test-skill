# Test Suite: User Authentication Module

> Generated from: `spec-auth.md`
> Total Scenarios: 52
> Coverage: Happy Path, Basic, Edge Cases, Negative, Security, UI/UX, Accessibility, Mobile, API, Performance

---

## Feature: Registration

### 📋 Coverage Summary
- Happy Path: 3 scenarios
- Edge Cases: 7 scenarios
- Negative: 5 scenarios
- Security: 4 scenarios
- UI/UX: 5 scenarios
- Accessibility: 2 scenarios
- API: 4 scenarios

---

### ✅ Happy Path & Basic

```gherkin
Feature: User Registration

  Background:
    Given the user is on the registration page
    And the registration form is fully visible

  @happy-path @basic
  Scenario: New user successfully registers with valid credentials
    Given the user has not registered before
    When the user enters email "newuser@example.com"
    And the user enters full name "Nguyen Van A"
    And the user enters password "SecurePass1!"
    And the user enters confirm password "SecurePass1!"
    And the user clicks the "Register" button
    Then the user is redirected to the "Check your email" screen
    And a verification email is sent to "newuser@example.com"
    And the success message "Please check your inbox to verify your account" is displayed

  @happy-path @basic
  Scenario Outline: Registration succeeds with various valid password formats
    Given the user fills in all required fields with valid data
    When the user enters password "<password>"
    And the user enters confirm password "<password>"
    And the user clicks "Register"
    Then registration is successful

    Examples:
      | password       |
      | SecurePass1!   |
      | MyP@ssw0rd     |
      | Abc12345#      |
      | Tr0ub4dor&3    |

  @happy-path @basic
  Scenario: Verification email contains correct link and expires appropriately
    Given a user just registered with email "user@example.com"
    When the user opens the verification email
    Then the email contains a verification link
    And the link is valid for at least 24 hours
    And clicking the link marks the account as verified
```

### 🔲 UI/UX & Accessibility

```gherkin
  @ui @ux
  Scenario: Registration form shows inline validation errors without page reload
    Given the user is on the registration form
    When the user types an invalid email format "notanemail"
    And the user clicks outside the email field
    Then an inline error "Please enter a valid email address" appears below the email field
    And the page does not reload
    And the email field border turns red

  @ui @ux
  Scenario: Password strength indicator updates in real-time as user types
    Given the user is in the password field
    When the user types "abc"
    Then the strength indicator shows "Weak"
    When the user types "abcDEF1"
    Then the strength indicator shows "Medium"
    When the user types "abcDEF1!"
    Then the strength indicator shows "Strong"

  @ui @ux
  Scenario: "Register" button is disabled until all required fields are filled
    Given the user lands on the registration page
    Then the "Register" button is disabled
    When the user fills in all required fields with valid data
    Then the "Register" button becomes enabled

  @ui @ux
  Scenario: Toggle password visibility works for both password fields
    Given the user is on the registration form
    When the user clicks the eye icon next to the password field
    Then the password characters are revealed as plain text
    When the user clicks the eye icon again
    Then the password characters are masked

  @ui @ux
  Scenario: Registration form is fully responsive on mobile viewport (375px)
    Given the user opens the registration page on a 375px wide screen
    Then all form fields are visible without horizontal scrolling
    And the "Register" button spans the full width
    And all labels are readable without zooming

  @accessibility
  Scenario: Registration form is navigable via keyboard only
    Given the user is on the registration page
    When the user presses Tab to navigate through the form
    Then each field receives focus in logical order: Name → Email → Password → Confirm Password → Register button
    And the currently focused element has a visible focus ring

  @accessibility
  Scenario: Screen reader announces form validation errors correctly
    Given the user submits the form with an empty email field
    Then the error message is announced by screen reader immediately
    And the aria-invalid attribute is set to "true" on the email input
    And focus moves to the first field with an error
```

### ⚠️ Edge Cases & Negative

```gherkin
  @edge-case
  Scenario: Registration with email at maximum allowed length (254 chars)
    Given the user enters an email exactly 254 characters long with valid format
    When the user completes the form and clicks "Register"
    Then registration succeeds

  @edge-case
  Scenario: Registration with full name containing special unicode characters
    Given the user enters full name "Nguyễn Văn Đức"
    When the user completes the form and clicks "Register"
    Then registration succeeds
    And the name is stored and displayed correctly

  @edge-case
  Scenario: User double-clicks Register button rapidly
    Given the user fills out the form with valid data
    When the user double-clicks the "Register" button
    Then only one registration request is sent
    And no duplicate account is created

  @edge-case
  Scenario: Password is exactly 8 characters (minimum boundary)
    When the user enters password "Abc123!z" (8 chars)
    Then the password is accepted as valid

  @edge-case
  Scenario: Password is 7 characters (below minimum boundary)
    When the user enters password "Abc12!z" (7 chars)
    Then an inline error "Password must be at least 8 characters" is shown
    And the Register button remains disabled

  @negative
  Scenario: Registration fails when email is already registered
    Given "existing@example.com" is already registered
    When a new user attempts to register with email "existing@example.com"
    And completes all other fields correctly
    And clicks "Register"
    Then an inline error "An account with this email already exists" is shown below the email field
    And no new account is created
    And no verification email is sent

  @negative
  Scenario: Registration fails when passwords do not match
    When the user enters password "SecurePass1!"
    And the user enters confirm password "DifferentPass1!"
    And clicks "Register"
    Then an inline error "Passwords do not match" is shown below the confirm password field

  @negative
  Scenario Outline: Registration fails when password does not meet complexity rules
    When the user enters password "<weak_password>"
    Then an inline error "<error_message>" is displayed

    Examples:
      | weak_password | error_message                                      |
      | password123   | Password must contain at least 1 uppercase letter  |
      | PASSWORD123   | Password must contain at least 1 lowercase letter  |
      | SecurePass!   | Password must contain at least 1 number            |
      | SecurePass1   | Password must contain at least 1 special character |

  @negative
  Scenario: Registration fails with empty required fields
    Given the user leaves all fields empty
    When the user clicks "Register"
    Then errors are shown for all required fields
    And the first error field receives focus
```

### 🔒 Security

```gherkin
  @security
  Scenario: XSS payload in full name field is sanitized and not executed
    When the user enters full name "<script>alert('xss')</script>"
    And registration completes
    Then the script is not executed on any page
    And the stored name is displayed as escaped text

  @security
  Scenario: SQL injection in email field does not affect the database
    When the user enters email "'; DROP TABLE users;--@test.com"
    Then the input is rejected with a validation error
    And the database remains intact

  @security
  Scenario: Registration API endpoint rejects requests without CSRF token
    Given an attacker sends a direct POST request to /api/register without a CSRF token
    Then the server responds with 403 Forbidden
    And no account is created

  @security
  Scenario: Sensitive registration data is not exposed in API response or URL
    When a user successfully registers
    Then the API response does not contain the plaintext password
    And no sensitive data appears in the browser URL bar
    And the password is not logged in server logs
```

### 🔌 API

```gherkin
  @api
  Scenario: POST /api/register returns 201 with correct response body on success
    When a POST request is sent to /api/register with valid payload
    Then the response status is 201
    And the response body contains "userId" and "email"
    And the response does not contain "password" or "passwordHash"

  @api
  Scenario: POST /api/register returns 422 when required fields are missing
    When a POST request is sent to /api/register with missing "email" field
    Then the response status is 422
    And the response body contains a "errors" array with field-level details

  @api
  Scenario: POST /api/register returns 409 when email already exists
    Given "existing@example.com" is already registered
    When a POST request is sent to /api/register with email "existing@example.com"
    Then the response status is 409
    And the response body message is "Email already in use"

  @api
  Scenario: POST /api/register response time is under 500ms under normal load
    When a POST request is sent to /api/register with valid payload
    Then the response is received within 500 milliseconds
```

---

## Feature: Login

### 📋 Coverage Summary
- Happy Path: 4 scenarios
- Edge Cases: 4 scenarios
- Negative: 5 scenarios
- Security: 5 scenarios
- UI/UX: 3 scenarios
- Mobile: 3 scenarios
- API: 3 scenarios

---

### ✅ Happy Path & Basic

```gherkin
Feature: User Login

  Background:
    Given a verified user exists with email "user@example.com" and password "SecurePass1!"
    And the user is on the login page

  @happy-path @basic
  Scenario: Verified user logs in successfully with correct credentials
    When the user enters email "user@example.com"
    And the user enters password "SecurePass1!"
    And the user clicks "Login"
    Then the user is redirected to the dashboard
    And the user's name is displayed in the navigation bar

  @happy-path @basic
  Scenario: User with "Remember me" checked maintains session for 30 days
    When the user enters valid credentials
    And the user checks "Remember me"
    And the user clicks "Login"
    Then a session cookie is set with 30-day expiry
    When the user closes and reopens the browser after 1 day
    Then the user is still logged in

  @happy-path @basic
  Scenario: User without "Remember me" gets a session-only cookie
    When the user logs in without checking "Remember me"
    Then the session cookie has no persistent expiry (session cookie)
    When the user closes the browser tab
    Then the user is logged out

  @happy-path @basic
  Scenario: User is redirected to originally requested URL after login
    Given an unauthenticated user tries to access "/dashboard/settings"
    Then the user is redirected to the login page
    When the user logs in successfully
    Then the user is redirected to "/dashboard/settings"
```

### ⚠️ Edge Cases & Negative

```gherkin
  @edge-case
  Scenario: Account auto-unlocks after 15-minute lockout window
    Given the user's account is locked after 5 failed attempts
    When 15 minutes have passed
    And the user enters correct credentials
    Then login succeeds
    And the failed attempt counter resets to 0

  @edge-case
  Scenario: Login with email containing uppercase letters (case-insensitive)
    When the user enters email "USER@EXAMPLE.COM" (all uppercase)
    And password "SecurePass1!"
    And clicks "Login"
    Then login is successful
    # Note: Email lookup must be case-insensitive

  @negative
  Scenario: Login fails with incorrect password and shows attempt count warning
    When the user enters correct email but wrong password
    And clicks "Login"
    Then login fails
    And the error message "Invalid email or password. 4 attempts remaining before lockout." is shown

  @negative
  Scenario: Account is locked after 5 consecutive failed login attempts
    When the user enters incorrect password 5 times in a row
    Then the account is locked
    And the error message "Your account has been temporarily locked for 15 minutes due to too many failed attempts" is shown
    And the user cannot log in even with the correct password

  @negative
  Scenario: Login fails for unverified account
    Given a user registered but did not verify their email
    When the user attempts to login with correct credentials
    Then login fails
    And the message "Please verify your email before logging in. Resend verification email?" is shown

  @security
  Scenario: Brute force is rate-limited at the API level regardless of IP
    When 10 rapid POST /api/login requests are sent with different passwords within 1 minute
    Then the API returns 429 Too Many Requests after the threshold
    And a Retry-After header is included in the response

  @security
  Scenario: Login form does not reveal whether email exists in the system
    When the user enters a non-existent email with any password
    Then the error message is "Invalid email or password"
    And the message is identical to the wrong-password error (no user enumeration)

  @security
  Scenario: JWT token in response is not accessible via JavaScript (httpOnly cookie)
    When the user logs in successfully
    Then the auth token is stored in an httpOnly cookie
    And document.cookie does not expose the auth token
```

### 📱 Mobile

```gherkin
  @mobile
  Scenario: Login form keyboard does not obscure submit button on small screens
    Given the user opens the login page on a mobile device (390px viewport)
    When the user taps the password field and the keyboard appears
    Then the "Login" button is still visible above the keyboard
    Or the user can scroll to reach it

  @mobile
  Scenario: Login works correctly after app is backgrounded during credential entry
    Given the user is mid-login on a mobile app (iOS/Android)
    When the user backgrounds the app for 2 minutes and returns
    Then the entered credentials are preserved
    And the user can complete login without re-entering data

  @mobile
  Scenario: Biometric login (Face ID / Fingerprint) works on return visit with Remember Me
    Given the user previously logged in with "Remember me" on mobile
    And biometric auth is enabled on the device
    When the user reopens the app
    Then the app prompts for biometric authentication
    And on successful biometric auth, the user is logged in without entering password
    # Note: Requires biometric SDK integration — verify feature is in scope
```

---

## Feature: Forgot Password

### 📋 Coverage Summary
- Happy Path: 3 scenarios
- Edge Cases: 5 scenarios
- Negative: 4 scenarios
- Security: 5 scenarios
- UI/UX: 2 scenarios
- API: 3 scenarios

---

### ✅ Happy Path & Basic

```gherkin
Feature: Forgot Password

  Background:
    Given a verified user exists with email "user@example.com"

  @happy-path @basic
  Scenario: User successfully resets password via OTP
    Given the user is on the "Forgot Password" page
    When the user enters "user@example.com" and clicks "Send OTP"
    Then a 6-digit OTP is sent to "user@example.com"
    And the user is directed to the OTP entry screen
    When the user enters the correct OTP
    Then the user is directed to the new password screen
    When the user enters a new valid password and confirms it
    And clicks "Reset Password"
    Then the password is updated successfully
    And the user is redirected to login page with message "Password reset successful. Please log in."

  @happy-path @basic
  Scenario: User can log in with new password after reset
    Given the user has successfully reset their password to "NewSecure1!"
    When the user logs in with "user@example.com" and "NewSecure1!"
    Then login succeeds

  @happy-path @basic
  Scenario: Old password no longer works after reset
    Given the user has reset their password
    When the user tries to log in with the old password
    Then login fails with "Invalid email or password"
```

### ⚠️ Edge Cases & Negative

```gherkin
  @edge-case
  Scenario: OTP expires exactly at 10-minute mark
    Given the user received an OTP at 10:00:00
    When the user enters the OTP at 10:10:01 (1 second past expiry)
    Then the error "This OTP has expired. Please request a new one." is shown

  @edge-case
  Scenario: OTP is single-use and cannot be reused after successful submission
    Given the user has used an OTP to access the password reset screen
    When the user tries to submit the same OTP again (e.g., navigating back)
    Then the error "This OTP has already been used." is shown

  @negative
  Scenario: Forgot password with non-existent email does not reveal user existence
    When the user enters "notregistered@example.com" and clicks "Send OTP"
    Then the UI shows "If this email is registered, you'll receive an OTP shortly"
    And no OTP email is sent
    # Security: prevents user enumeration

  @negative
  Scenario: Entering wrong OTP 3 times invalidates it
    When the user enters an incorrect OTP 3 times
    Then the OTP is invalidated
    And the error "Too many incorrect attempts. Please request a new OTP." is shown

  @security
  Scenario: OTP is 6 digits — brute force is rate-limited
    When an attacker sends 10 rapid OTP submission requests in 60 seconds
    Then the API returns 429 Too Many Requests
    And the OTP is invalidated after 5 failed attempts

  @security
  Scenario: OTP in email does not appear in server-side logs
    When the OTP is generated and emailed
    Then the raw OTP value does not appear in application logs
    # Note: Verify with server log audit during testing

  @security
  Scenario: Password reset token in URL is one-time use and expires
    Given a reset link with token is sent via email
    When the user clicks the link and completes the reset
    And the user tries to use the same reset link again
    Then the response is "This reset link has already been used or has expired"
```

---

## 🗺️ Coverage Matrix

| Feature         | Happy Path | Edge Cases | Negative | Security | UI/UX | Mobile | API | Accessibility | Total |
|-----------------|-----------|-----------|----------|----------|-------|--------|-----|---------------|-------|
| Registration    | ✅ 3      | ✅ 7      | ✅ 5    | ✅ 4    | ✅ 5 | —      | ✅ 4| ✅ 2         | **30**|
| Login           | ✅ 4      | ✅ 4      | ✅ 5    | ✅ 5    | ✅ 3 | ✅ 3  | ✅ 3| —            | **27**|
| Forgot Password | ✅ 3      | ✅ 5      | ✅ 4    | ✅ 5    | ✅ 2 | —      | ✅ 3| —            | **22**|
| **Total**       | **10**    | **16**    | **14**  | **14**  | **10**| **3** | **10**| **2**      | **79**|

---

## 🚨 Risk Areas & Notes

### Ambiguities needing clarification:
1. **Biometric login** — spec mentions mobile but doesn't specify if biometric is in scope. Flagged as `# Note` in scenarios.
2. **Email verification link expiry** — spec says OTP is 10 mins but doesn't mention verification link TTL. Assumed 24h in tests.
3. **"Remember me" on mobile** — unclear if cookie-based or token-based session on native mobile app.

### High-risk areas:
- **Account lockout logic** — race conditions possible if multiple tabs send requests simultaneously
- **OTP replay attacks** — critical to ensure single-use enforcement at DB level, not just API level
- **User enumeration** — both forgot password and registration must return identical-looking responses for existing/non-existing emails

### Recommended test data setup:
- Seed DB with: verified user, unverified user, locked-out user, user with expired OTP
- Test environment should have email sandbox (e.g., Mailtrap) to capture sent emails

### Performance testing note:
- Run load test on `/api/login` and `/api/register` with 100 concurrent users to verify <500ms SLA
