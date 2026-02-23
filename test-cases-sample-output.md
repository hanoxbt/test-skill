# Test Suite: User Authentication Module

> Generated from: `spec-auth.md`
> Total Scenarios: 79
> Coverage: Happy Path, Basic, Edge Cases, Negative, Security, UI/UX, Accessibility, Mobile, API

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
- **Total: 30 scenarios**

---

### ✅ Happy Path & Basic

```gherkin
Feature: User Registration

  Background:
    Given the user is on the registration page
    And the registration form is fully visible

  @happy-path @basic @critical
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

  @happy-path @basic @critical
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

  @happy-path @basic @critical
  Scenario: Verification email contains correct link and expires appropriately
    Given a user just registered with email "user@example.com"
    When the user opens the verification email
    Then the email contains a verification link
    And the link is valid for at least 24 hours
    And clicking the link marks the account as verified
```

---

### 🔲 UI/UX & Accessibility

```gherkin
  @ui @ux @minor
  Scenario: Registration form shows inline validation errors without page reload
    Given the user is on the registration form
    When the user types an invalid email format "notanemail"
    And the user clicks outside the email field
    Then an inline error "Please enter a valid email address" appears below the email field
    And the page does not reload
    And the email field border turns red

  @ui @ux @minor
  Scenario: Password strength indicator updates in real-time as user types
    Given the user is in the password field
    When the user types "abc"
    Then the strength indicator shows "Weak"
    When the user types "abcDEF1"
    Then the strength indicator shows "Medium"
    When the user types "abcDEF1!"
    Then the strength indicator shows "Strong"

  @ui @ux @minor
  Scenario: "Register" button is disabled until all required fields are filled
    Given the user lands on the registration page
    Then the "Register" button is disabled
    When the user fills in all required fields with valid data
    Then the "Register" button becomes enabled

  @ui @ux @minor
  Scenario: Toggle password visibility works for both password fields
    Given the user is on the registration form
    When the user clicks the eye icon next to the password field
    Then the password characters are revealed as plain text
    When the user clicks the eye icon again
    Then the password characters are masked

  @ui @ux @minor
  Scenario: Registration form is fully responsive on mobile viewport (375px)
    Given the user opens the registration page on a 375px wide screen
    Then all form fields are visible without horizontal scrolling
    And the "Register" button spans the full width
    And all labels are readable without zooming

  @accessibility @minor
  Scenario: Registration form is navigable via keyboard only
    Given the user is on the registration page
    When the user presses Tab to navigate through the form
    Then each field receives focus in logical order: Name → Email → Password → Confirm Password → Register button
    And the currently focused element has a visible focus ring

  @accessibility @minor
  Scenario: Screen reader announces form validation errors correctly
    Given the user submits the form with an empty email field
    Then the error message is announced by screen reader immediately
    And the aria-invalid attribute is set to "true" on the email input
    And focus moves to the first field with an error
```

---

### ⚠️ Edge Cases & Negative

```gherkin
  @edge-case @major
  Scenario: Registration with email at maximum allowed length (254 chars)
    Given the user enters an email exactly 254 characters long with valid format
    When the user completes the form and clicks "Register"
    Then registration succeeds

  @edge-case @major
  Scenario: Registration with full name containing special unicode characters
    Given the user enters full name "Nguyễn Văn Đức"
    When the user completes the form and clicks "Register"
    Then registration succeeds
    And the name is stored and displayed correctly

  @edge-case @major
  Scenario: User double-clicks Register button rapidly
    Given the user fills out the form with valid data
    When the user double-clicks the "Register" button
    Then only one registration request is sent
    And no duplicate account is created

  @edge-case @major
  Scenario: Password is exactly 8 characters (minimum boundary)
    When the user enters password "Abc123!z" (8 chars)
    Then the password is accepted as valid

  @edge-case @major
  Scenario: Password is 7 characters (below minimum boundary)
    When the user enters password "Abc12!z" (7 chars)
    Then an inline error "Password must be at least 8 characters" is shown
    And the Register button remains disabled

  @edge-case @major
  Scenario: Registration with password at maximum allowed length (128 chars)
    When the user enters a 128-character password that meets all complexity rules
    And the user completes the form and clicks "Register"
    Then registration succeeds
    # Note: Confirm max password length with dev team — flag as TBD if not specified in spec

  @edge-case @major
  Scenario: Authenticated user navigating to registration page is redirected
    Given the user is already logged in
    When the user navigates directly to the registration page URL
    Then the user is redirected to the dashboard
    And the registration form is not shown

  @negative @major
  Scenario: Registration fails when email is already registered
    Given "existing@example.com" is already registered
    When a new user attempts to register with email "existing@example.com"
    And completes all other fields correctly
    And clicks "Register"
    Then an inline error "An account with this email already exists" is shown below the email field
    And no new account is created
    And no verification email is sent

  @negative @major
  Scenario: Registration fails when passwords do not match
    When the user enters password "SecurePass1!"
    And the user enters confirm password "DifferentPass1!"
    And clicks "Register"
    Then an inline error "Passwords do not match" is shown below the confirm password field

  @negative @major
  Scenario Outline: Registration fails when password does not meet complexity rules
    When the user enters password "<weak_password>"
    Then an inline error "<error_message>" is displayed

    Examples:
      | weak_password | error_message                                      |
      | password123   | Password must contain at least 1 uppercase letter  |
      | PASSWORD123   | Password must contain at least 1 lowercase letter  |
      | SecurePass!   | Password must contain at least 1 number            |
      | SecurePass1   | Password must contain at least 1 special character |

  @negative @major
  Scenario: Registration fails with empty required fields
    Given the user leaves all fields empty
    When the user clicks "Register"
    Then errors are shown for all required fields
    And the first error field receives focus

  @negative @major
  Scenario: Registration fails when full name contains only whitespace
    When the user enters full name "     " (whitespace only)
    And completes all other fields with valid data
    And clicks "Register"
    Then an inline error "Full name cannot be blank" is shown
    And no account is created
```

---

### 🔒 Security

```gherkin
  @security @critical
  Scenario: XSS payload in full name field is sanitized and not executed
    When the user enters full name "<script>alert('xss')</script>"
    And registration completes
    Then the script is not executed on any page
    And the stored name is displayed as escaped text

  @security @critical
  Scenario: SQL injection in email field does not affect the database
    When the user enters email "'; DROP TABLE users;--@test.com"
    Then the input is rejected with a validation error
    And the database remains intact

  @security @critical
  Scenario: Registration API endpoint rejects requests without CSRF token
    Given an attacker sends a direct POST request to /api/register without a CSRF token
    Then the server responds with 403 Forbidden
    And no account is created

  @security @critical
  Scenario: Sensitive registration data is not exposed in API response or URL
    When a user successfully registers
    Then the API response does not contain the plaintext password
    And no sensitive data appears in the browser URL bar
    And the password is not logged in server logs
```

---

### 🔌 API

```gherkin
  @api @major
  Scenario: POST /api/register returns 201 with correct response body on success
    When a POST request is sent to /api/register with valid payload
    Then the response status is 201
    And the response body contains "userId" and "email"
    And the response does not contain "password" or "passwordHash"

  @api @major
  Scenario: POST /api/register returns 422 when required fields are missing
    When a POST request is sent to /api/register with missing "email" field
    Then the response status is 422
    And the response body contains a "errors" array with field-level details

  @api @major
  Scenario: POST /api/register returns 409 when email already exists
    Given "existing@example.com" is already registered
    When a POST request is sent to /api/register with email "existing@example.com"
    Then the response status is 409
    And the response body message is "Email already in use"

  @api @major
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
- **Total: 27 scenarios**

---

### ✅ Happy Path & Basic

```gherkin
Feature: User Login

  Background:
    Given a verified user exists with email "user@example.com" and password "SecurePass1!"
    And the user is on the login page

  @happy-path @basic @critical
  Scenario: Verified user logs in successfully with correct credentials
    When the user enters email "user@example.com"
    And the user enters password "SecurePass1!"
    And the user clicks "Login"
    Then the user is redirected to the dashboard
    And the user's name is displayed in the navigation bar

  @happy-path @basic @critical
  Scenario: User with "Remember me" checked maintains session for 30 days
    When the user enters valid credentials
    And the user checks "Remember me"
    And the user clicks "Login"
    Then a session cookie is set with 30-day expiry
    When the user closes and reopens the browser after 1 day
    Then the user is still logged in

  @happy-path @basic @major
  Scenario: User without "Remember me" gets a session-only cookie
    When the user logs in without checking "Remember me"
    Then the session cookie has no persistent expiry (session cookie)
    When the user closes the browser tab
    Then the user is logged out

  @happy-path @basic @critical
  Scenario: User is redirected to originally requested URL after login
    Given an unauthenticated user tries to access "/dashboard/settings"
    Then the user is redirected to the login page
    When the user logs in successfully
    Then the user is redirected to "/dashboard/settings"
```

---

### 🔲 UI/UX

```gherkin
  @ui @ux @minor
  Scenario: Login button is disabled until both email and password fields are filled
    Given the user is on the login page
    Then the "Login" button is disabled
    When the user fills in both email and password fields
    Then the "Login" button becomes enabled

  @ui @ux @minor
  Scenario: Login form shows a loading spinner while request is in progress
    When the user submits valid credentials
    Then the "Login" button shows a loading spinner
    And the button is disabled during the request
    And the loading state clears once the response is received

  @ui @ux @minor
  Scenario: Login page is fully responsive on tablet viewport (768px)
    Given the user opens the login page on a 768px wide screen
    Then all form elements are properly aligned and readable
    And the "Login" button is easily tappable
    And no horizontal scrolling is required
```

---

### ⚠️ Edge Cases & Negative

```gherkin
  @edge-case @major
  Scenario: Account auto-unlocks after 15-minute lockout window
    Given the user's account is locked after 5 failed attempts
    When 15 minutes have passed
    And the user enters correct credentials
    Then login succeeds
    And the failed attempt counter resets to 0

  @edge-case @major
  Scenario: Login with email containing uppercase letters succeeds (case-insensitive)
    When the user enters email "USER@EXAMPLE.COM" (all uppercase)
    And password "SecurePass1!"
    And clicks "Login"
    Then login is successful
    # Note: Email lookup must be case-insensitive at the database level

  @edge-case @major
  Scenario: Login form retains email value but clears password after a failed attempt
    When the user enters email "user@example.com" and an incorrect password
    And clicks "Login"
    Then login fails
    And the email field still contains "user@example.com"
    And the password field is cleared

  @edge-case @major
  Scenario: Login succeeds when email has leading or trailing whitespace
    When the user enters email "  user@example.com  " with surrounding spaces
    And enters the correct password
    And clicks "Login"
    Then login is successful
    # Note: Email must be trimmed before lookup — verify server-side handling

  @negative @critical
  Scenario: Login fails with incorrect password and shows remaining attempt warning
    When the user enters correct email but wrong password
    And clicks "Login"
    Then login fails
    And the error message "Invalid email or password. 4 attempts remaining before lockout." is shown

  @negative @critical
  Scenario: Account is locked after 5 consecutive failed login attempts
    When the user enters incorrect password 5 times in a row
    Then the account is locked
    And the error message "Your account has been temporarily locked for 15 minutes due to too many failed attempts" is shown
    And the user cannot log in even with the correct password

  @negative @critical
  Scenario: Login fails for unverified account with prompt to resend email
    Given a user registered but did not verify their email
    When the user attempts to login with correct credentials
    Then login fails
    And the message "Please verify your email before logging in. Resend verification email?" is shown

  @negative @major
  Scenario: Login fails when email field is submitted empty
    When the user leaves the email field empty and enters a password
    And clicks "Login"
    Then an inline error "Email is required" is shown
    And no login request is sent to the server

  @negative @major
  Scenario: Login fails when password field is submitted empty
    When the user enters a valid email but leaves the password field empty
    And clicks "Login"
    Then an inline error "Password is required" is shown
    And no login request is sent to the server
```

---

### 🔒 Security

```gherkin
  @security @critical
  Scenario: Brute force is rate-limited at the API level regardless of IP rotation
    When 10 rapid POST /api/login requests are sent with different passwords within 1 minute
    Then the API returns 429 Too Many Requests after the threshold
    And a Retry-After header is included in the response

  @security @critical
  Scenario: Login form does not reveal whether an email exists in the system
    When the user enters a non-existent email with any password
    Then the error message is "Invalid email or password"
    And the message is identical to the wrong-password error
    # Security: prevents user enumeration via login form

  @security @critical
  Scenario: JWT token in response is stored in httpOnly cookie, not accessible via JavaScript
    When the user logs in successfully
    Then the auth token is stored in an httpOnly cookie
    And document.cookie in the browser does not expose the auth token

  @security @critical
  Scenario: Login API response does not leak user account data on failure
    When a POST request is sent to /api/login with valid email but wrong password
    Then the response status is 401
    And the response body does not contain any user account information
    And response headers do not include user-identifying data

  @security @critical
  Scenario: Login form is protected against CSRF — POST requires valid CSRF token
    Given an attacker crafts a forged POST request to /api/login without a CSRF token
    Then the server responds with 403 Forbidden
    And no session is created
```

---

### 📱 Mobile

```gherkin
  @mobile @major
  Scenario: Login form keyboard does not obscure the submit button on small screens
    Given the user opens the login page on a mobile device (390px viewport)
    When the user taps the password field and the software keyboard appears
    Then the "Login" button is still visible above the keyboard
    Or the user can scroll to reach the button without dismissing the keyboard

  @mobile @major
  Scenario: Login works correctly after app is backgrounded during credential entry
    Given the user is mid-login on a mobile app (iOS/Android)
    When the user backgrounds the app for 2 minutes and returns
    Then the entered credentials are preserved
    And the user can complete login without re-entering data

  @mobile @minor
  Scenario: Biometric login (Face ID / Fingerprint) works on return visit with Remember Me
    Given the user previously logged in with "Remember me" on mobile
    And biometric authentication is enabled on the device
    When the user reopens the app
    Then the app prompts for biometric authentication
    And on successful biometric auth the user is logged in without entering a password
    # Note: Requires biometric SDK integration — confirm feature is in scope before testing
```

---

### 🔌 API

```gherkin
  @api @critical
  Scenario: POST /api/login returns 200 with auth token set in httpOnly cookie on success
    When a POST request is sent to /api/login with valid credentials
    Then the response status is 200
    And the response sets an httpOnly cookie containing the auth token
    And the response body contains "userId" and "email"
    And the response body does not contain "password"

  @api @major
  Scenario: POST /api/login returns 401 with generic error message on invalid credentials
    When a POST request is sent to /api/login with a wrong password
    Then the response status is 401
    And the response body contains message "Invalid email or password"
    And no auth token is set in cookies

  @api @major
  Scenario: POST /api/login returns 429 with Retry-After header when rate limit is exceeded
    When more than 10 POST requests are sent to /api/login within 60 seconds
    Then subsequent requests return status 429 Too Many Requests
    And the response includes a "Retry-After" header
    And the response body explains the lockout duration
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
- **Total: 22 scenarios**

---

### ✅ Happy Path & Basic

```gherkin
Feature: Forgot Password

  Background:
    Given a verified user exists with email "user@example.com"

  @happy-path @basic @critical
  Scenario: User successfully resets password via OTP end-to-end
    Given the user is on the "Forgot Password" page
    When the user enters "user@example.com" and clicks "Send OTP"
    Then a 6-digit OTP is sent to "user@example.com"
    And the user is directed to the OTP entry screen
    When the user enters the correct OTP
    Then the user is directed to the new password screen
    When the user enters a new valid password and confirms it
    And clicks "Reset Password"
    Then the password is updated successfully
    And the user is redirected to the login page with message "Password reset successful. Please log in."

  @happy-path @basic @critical
  Scenario: User can log in with new password immediately after reset
    Given the user has successfully reset their password to "NewSecure1!"
    When the user logs in with "user@example.com" and "NewSecure1!"
    Then login succeeds

  @happy-path @basic @critical
  Scenario: Old password no longer works after successful reset
    Given the user has reset their password
    When the user tries to log in with the old password "SecurePass1!"
    Then login fails with "Invalid email or password"
```

---

### 🔲 UI/UX

```gherkin
  @ui @ux @minor
  Scenario: OTP input screen displays a live countdown timer for remaining validity
    Given the user has been sent an OTP
    When the user is on the OTP entry screen
    Then a countdown timer shows the remaining validity time in real-time
    And when the timer reaches 0 the form shows "OTP expired — please request a new one"

  @ui @ux @minor
  Scenario: "Resend OTP" button is disabled for 60 seconds after initial send
    Given the user is on the OTP entry screen
    Then the "Resend OTP" button is disabled
    And a label "Resend in 45s" counts down in real-time
    When 60 seconds have passed
    Then the "Resend OTP" button becomes active
```

---

### ⚠️ Edge Cases & Negative

```gherkin
  @edge-case @major
  Scenario: OTP expires exactly 1 second past the 10-minute mark
    Given the user received an OTP at 10:00:00
    When the user enters the OTP at 10:10:01
    Then the error "This OTP has expired. Please request a new one." is shown

  @edge-case @major
  Scenario: OTP is single-use and cannot be reused after successful submission
    Given the user has used an OTP to access the password reset screen
    When the user navigates back and tries to submit the same OTP again
    Then the error "This OTP has already been used." is shown

  @edge-case @major
  Scenario: Requesting a new OTP invalidates the previously issued OTP
    Given the user has received OTP "123456"
    When the user clicks "Resend OTP" and a new OTP is issued
    And the user tries to enter the old OTP "123456"
    Then the error "This OTP is no longer valid. Please use the most recent one." is shown

  @edge-case @major
  Scenario: New password cannot be identical to the current password
    Given the user has verified OTP and is on the new password screen
    When the user enters their current password as the new password
    And clicks "Reset Password"
    Then the error "New password cannot be the same as your current password" is shown

  @edge-case @major
  Scenario: Reset link accessed within its validity window completes successfully
    Given a password reset link was generated 29 minutes and 59 seconds ago (TTL is 30 minutes)
    When the user clicks the link
    Then the password reset form is accessible
    And the user can complete the reset
    # Note: Test token TTL boundary — confirm exact TTL with dev team

  @negative @major
  Scenario: Forgot password submitted with non-existent email does not reveal user existence
    When the user enters "notregistered@example.com" and clicks "Send OTP"
    Then the UI shows "If this email is registered, you'll receive an OTP shortly"
    And no OTP email is sent
    # Security: identical response prevents email enumeration

  @negative @major
  Scenario: Entering wrong OTP 3 times invalidates it and blocks further attempts
    When the user enters an incorrect OTP 3 times consecutively
    Then the OTP is invalidated
    And the error "Too many incorrect attempts. Please request a new OTP." is shown

  @negative @major
  Scenario: OTP entry field rejects non-numeric characters
    When the user types letters or special characters into the OTP field
    Then non-numeric characters are blocked or stripped
    And the field enforces numeric-only input

  @negative @major
  Scenario: Password reset fails when the new passwords do not match
    Given the user has verified OTP and is on the new password screen
    When the user enters new password "NewPass1!" and confirm password "DiffPass1!"
    And clicks "Reset Password"
    Then an error "Passwords do not match" is shown below the confirm field
    And the password is not updated
```

---

### 🔒 Security

```gherkin
  @security @critical
  Scenario: OTP brute force is rate-limited — API blocks after 5 failed attempts
    When an attacker sends 10 rapid OTP submission requests within 60 seconds
    Then the API returns 429 Too Many Requests after the 5th failed attempt
    And the OTP is invalidated
    And further guessing is blocked

  @security @critical
  Scenario: OTP value is not exposed in server-side application logs
    When the OTP is generated and emailed to the user
    Then the raw OTP value does not appear in application or server logs
    # Note: Verify with log audit during testing — requires DevOps coordination

  @security @critical
  Scenario: Password reset link is one-time use and cannot be replayed
    Given a reset link with a token has been sent via email
    When the user clicks the link and completes the reset successfully
    And the user (or attacker) tries to use the same reset link again
    Then the response shows "This reset link has already been used or has expired"

  @security @critical
  Scenario: OTP is delivered only to the registered email — never exposed in API response
    When the user requests an OTP reset for "user@example.com"
    Then the API response does not contain the OTP value
    And the response only confirms "OTP sent successfully"
    And the OTP is sent exclusively to the registered email address

  @security @critical
  Scenario: Direct access to the new password screen without a valid OTP session is blocked
    Given an attacker attempts to access the password reset form directly via URL
    When the request does not include a valid OTP session token
    Then the server returns 403 Forbidden
    And no password can be reset
```

---

### 🔌 API

```gherkin
  @api @major
  Scenario: POST /api/forgot-password always returns 200 regardless of whether email exists
    When a POST request is sent to /api/forgot-password with any email address
    Then the response is always 200 OK
    And the response body always reads "If this email is registered, you'll receive an OTP shortly"
    # Security: consistent response prevents user enumeration via API

  @api @critical
  Scenario: POST /api/verify-otp returns 200 and a temporary session token on correct OTP
    When a POST request is sent to /api/verify-otp with a valid OTP
    Then the response status is 200
    And a temporary password-reset session token is returned in the response
    And the token expires after 10 minutes

  @api @major
  Scenario: POST /api/reset-password returns 400 when the reset session token has expired
    Given a password reset session token has expired
    When a POST request is sent to /api/reset-password with the expired token
    Then the response status is 400
    And the response message is "Reset session expired. Please restart the forgot password flow."
```

---

## 🗺️ Coverage Matrix

| Feature         | Happy Path | Edge Cases | Negative | Security | UI/UX | A11y | Mobile | API | Total |
|-----------------|-----------|-----------|----------|----------|-------|------|--------|-----|-------|
| Registration    | ✅ 3      | ✅ 7      | ✅ 5    | ✅ 4    | ✅ 5 | ✅ 2 | —      | ✅ 4| **30**|
| Login           | ✅ 4      | ✅ 4      | ✅ 5    | ✅ 5    | ✅ 3 | —    | ✅ 3  | ✅ 3| **27**|
| Forgot Password | ✅ 3      | ✅ 5      | ✅ 4    | ✅ 5    | ✅ 2 | —    | —      | ✅ 3| **22**|
| **Total**       | **10**    | **16**    | **14**  | **14**  | **10**| **2**| **3** | **10**| **79**|

### Priority Distribution

| Priority | Count | % |
|---|---|---|
| 🔴 Critical | 33 | 42% |
| 🟡 Major    | 33 | 42% |
| 🟢 Minor    | 13 | 16% |
| **Total**   | **79** | 100% |

---

## 🚨 Risk Areas & Notes

### Ambiguities needing clarification:
1. **Biometric login** — spec mentions mobile but doesn't specify if biometric is in scope. Flagged as `# Note` in scenario — confirm before testing.
2. **Email verification link expiry** — spec defines OTP TTL as 10 mins but doesn't mention verification link TTL. Assumed 24h in tests — confirm with dev team.
3. **"Remember me" on mobile** — unclear if cookie-based or token-based session on native mobile. Behavior may differ from web.
4. **Maximum password length** — spec does not specify an upper limit. Assumed 128 chars — confirm and update `[TBD]` scenarios.
5. **Reset link TTL** — spec doesn't mention how long a password reset link stays valid. Assumed 30 minutes — must confirm.

### High-risk areas:
- **Account lockout logic** — race condition possible if concurrent requests from multiple tabs all hit the threshold simultaneously
- **OTP replay attacks** — single-use enforcement must be at DB level, not just API response level
- **User enumeration** — registration, login failure, and forgot password must all return visually identical responses for existing vs non-existing emails
- **CSRF protection** — all POST endpoints (register, login, forgot-password, reset-password) must validate CSRF token

### Recommended test data setup:
- Seed DB with: verified user, unverified user, locked-out user, user with expired OTP, user with used OTP
- Test environment must have email sandbox (e.g., Mailtrap) to capture and inspect sent emails
- For security tests: test with both authenticated and unauthenticated sessions

### Performance testing note:
- Run load test on `/api/login` and `/api/register` with 100 concurrent users to verify <500ms SLA
- Verify lockout counter is atomic under concurrent requests (no race condition bypass)
