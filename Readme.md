# Respondex — NativePHP Mobile Authentication MVP

Act as a senior Laravel, NativePHP Mobile, Android, and application-security engineer. Build the first working authentication release of **Respondex**, a mobile application intended for emergency response organizations such as MDRRMO, police, fire services, ambulance teams, dispatchers, and authorized emergency personnel.

> **Repository layout:** the mobile app lives in [`mobile/`](mobile/) and the backend API in [`api/`](api/). In this document, `respondex-mobile` refers to `mobile/` and `respondex-api` refers to `api/`.

## 1. Inspect the existing environment first

Before modifying files:

- Inspect both `respondex-mobile` and `respondex-api`.
- Identify the actual Laravel, PHP, NativePHP Mobile, NativePHP UI/EDGE, and Sanctum versions.
- Read the documentation that matches the installed versions.
- Confirm the correct NativePHP EDGE components, form inputs, buttons, navigation, HTTP networking, and secure-storage APIs available in this installation.
- Inspect the existing routes, models, middleware, configuration, migrations, and tests.
- Confirm whether the application can make HTTPS/API requests and persist authentication securely on Android.
- Identify missing dependencies and configuration.

Do not invent NativePHP APIs or assume that browser-based Blade, JavaScript, Tailwind CSS, or HTML components behave like native EDGE components. Do not upgrade major dependencies without explaining why and obtaining approval.

## 2. Project architecture

Use two separate applications.

**Mobile application — `respondex-mobile`**
- Laravel with NativePHP Mobile.
- Blade views and NativePHP EDGE UI components.
- Android as the first target.
- Use Tailwind CSS only where supported by the actual rendering system.
- Communicate with the separate API.
- Never connect directly to MySQL.

**Backend API — `respondex-api`**
- Laravel API routes.
- MySQL database.
- Laravel Sanctum personal access tokens for mobile authentication.
- Server-side password hashing, validation, authorization, email verification, and password reset.
- JSON responses with consistent validation and error formats.

Keep each application's responsibilities separate and follow existing Laravel conventions.

## 3. Authentication screens

Create a polished, responsive, professional authentication experience for Respondex.

### Login
- Email address.
- Password.
- Show/hide password if supported.
- Remember me, with a clearly defined secure persistence policy.
- Sign-in button with loading and disabled states.
- Link to registration.
- Link to forgot password.
- Field validation and accessible error messages.
- Friendly messages for incorrect credentials, network failures, and expired sessions.

### Registration
- Full name.
- Email address.
- Password and confirmation.
- Password-strength and validation feedback.
- Acceptance of any required organization policy or terms.
- Clear indication that a new responder account may require administrative approval.

Never allow a public registrant to select or grant themselves an administrator, dispatcher, or supervisor role. Use a restricted default role and pending-approval status where appropriate.

### Forgot and reset password
- Request a password-reset email.
- Display a generic confirmation to avoid revealing whether an email address is registered.
- Implement the actual backend reset flow using Laravel's password-reset mechanisms.
- Validate reset tokens, expiry, and new-password confirmation.
- Explain how reset links are handled on mobile, using a supported and secure approach.

### Email verification
- Verification instructions screen.
- Resend-verification action with rate limiting.
- Verification status and success/error handling.
- Enforce verification where required by the API's account policy.

### Authenticated dashboard
Create a simple first dashboard showing:
- Respondex branding.
- Signed-in user's name.
- Assigned role and account/approval status.
- A clear "Authentication successful" state.
- Profile/settings entry.
- Logout button.

Do not implement dispatch, live GPS tracking, incident management, maps, or vehicle management in this MVP.

## 4. Backend API functionality

Implement and test the appropriate API endpoints for:

- User registration.
- Login and token issuance.
- Current authenticated user.
- Logout and current-device token revocation.
- Profile retrieval and permitted profile updates.
- Password-reset request and reset submission.
- Email verification and verification resend.
- Role and account-status checks.

Use Laravel Sanctum and the correct `auth:sanctum` middleware for protected endpoints.

Use Laravel's secure password hashing and validation. Return structured JSON responses with appropriate HTTP status codes.

Enforce authorization on the backend, not merely by hiding buttons in the mobile interface. Protect privileged endpoints by role and account status.

Use rate limiting for login, registration, reset-password, and verification endpoints. Avoid exposing sensitive account details in error messages.

Do not issue tokens to unapproved or suspended accounts. Define and test the rules for verified, pending, approved, and suspended users.

## 5. Secure mobile authentication

- Build a reusable API client with a configurable API base URL.
- Support development and production configuration without hardcoding production secrets.
- Send tokens using `Authorization: Bearer <token>`.
- Verify the supported NativePHP secure-storage API before implementing token persistence.
- Store authentication tokens only in appropriate secure storage; never store passwords or tokens in plaintext preferences or logs.
- Implement remember-me behavior without weakening token security.
- Handle expired/revoked tokens, failed requests, offline states, and server errors.
- Clear the local token on logout and revoke the server-side token.
- Ensure protected screens cannot be used as proof of authorization without server validation.
- Use HTTPS in production and never disable TLS certificate validation.

If the installed NativePHP version does not provide a suitable secure-storage capability, explain the limitation and propose a compatible solution before implementing an insecure workaround.

## 6. Visual design

Use a clean, trustworthy emergency-services design:

- Light background, navy primary color, restrained orange accent.
- Strong typography and clear information hierarchy.
- Accessible contrast and readable form labels.
- Large touch targets suitable for mobile devices.
- Consistent spacing, field states, loading indicators, and error states.
- Professional Respondex wordmark treatment; do not invent an official government seal or agency endorsement.
- Use native EDGE controls wherever appropriate.
- Avoid unnecessary animations, excessive gradients, and clutter.

Ensure the screens use components and styling supported by the actual NativePHP Mobile installation.

## 7. Database and configuration

Create only the migrations and models necessary for authentication and authorization.

Use existing Laravel user, password-reset, verification, and Sanctum structures when available instead of creating duplicate systems.

Define the role and approval strategy explicitly. Initial roles are:

- `responder`
- `dispatcher`
- `supervisor`
- `administrator`

New public registrations must never receive privileged roles automatically. Provide a secure method for administrators to approve users, and do not create a hardcoded production administrator password.

Keep database credentials, mail credentials, signing secrets, and other private configuration on the API server.

## 8. Testing and acceptance criteria

Add automated backend tests and practical mobile test instructions covering:

1. Successful login.
2. Incorrect credentials.
3. Missing or invalid fields.
4. Registration and duplicate email addresses.
5. Attempted role escalation during registration.
6. Password reset and invalid/expired reset tokens.
7. Email verification and resend throttling.
8. Unapproved and suspended accounts.
9. Protected endpoints with missing, invalid, expired, and revoked tokens.
10. Logout and token revocation.
11. Remember-me behavior and secure token persistence.
12. Network errors and unreachable API.
13. Android emulator and physical Android device connectivity.
14. API authorization for every privileged role.

Run the available tests and report the actual results. Never claim a test passed unless you ran it successfully.

## 9. Implementation workflow

Work incrementally:

| Phase | Scope |
|-------|-------|
| **A** | Inspect both projects, versions, documentation, and configuration. Report the proposed implementation plan and any blockers. |
| **B** | Implement the backend database, authentication endpoints, validation, token handling, roles, and tests. |
| **C** | Implement the NativePHP login, registration, recovery, verification, dashboard, profile, and logout screens. |
| **D** | Connect the mobile app to the API and verify secure token persistence and Android networking. |
| **E** | Run tests, fix errors, and document the working setup. |

After each phase, summarize the files changed, commands run, test results, remaining issues, and the next steps.

Do not replace existing configuration blindly. Do not generate unnecessary files or dependencies. Preserve a clear separation between the mobile app and API.

**Start with Phase A. Inspect the actual projects and environment first, then provide the plan and blockers before making changes.**
