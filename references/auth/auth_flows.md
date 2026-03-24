# Auth Flows — FDK React Templates

Source: `src/pages/login/`, `src/page-layouts/login/`, `src/page-layouts/auth/`

## Architecture Overview

```
src/pages/login/login.jsx           ← Page entry, orchestrates all login views
src/page-layouts/login/component/
  login-password/                   ← Email + password form
  login-otp/                        ← Mobile OTP form (get OTP → verify)
  login-mode-button/                ← Toggle between Password ↔ OTP modes
  term-privacy/                     ← T&C consent checkbox
  soacial-login-button/             ← Google / Facebook / Apple (legacy dir)
  social-login-button/              ← Google / Facebook / Apple (current dir)
src/page-layouts/auth/
  login-register-toggle/            ← Button to switch Login → Register
  mobile-number/                    ← Reusable phone input with country code
  verify-both/                      ← OTP verification for mobile + email
  account-locked/                   ← Locked account state display
```

---

## 1. Login Page (`src/pages/login/login.jsx`)

Entry point for all auth flows. Controlled by parent (page resolver) via props.

### Key Props

| Prop | Type | Purpose |
|------|------|---------|
| `isPassword` | boolean | Show password login form |
| `isOtp` | boolean | Show OTP login form |
| `isRegisterEnabled` | boolean | Show "Go to Register" button |
| `showLoginToggleButton` | boolean | Show Password ↔ OTP mode toggle |
| `social` | `{google, facebook, apple}` | Enable social login buttons |
| `isFormSubmitSuccess` | boolean | Hides header/footer after OTP sent |
| `isTermsAccepted` | boolean (internal) | Consent gate before form submit |

### Flow

```
Login page load
  ├── isPassword=true  → LoginPassword form (email + password)
  └── isOtp=true       → LoginOtp form (mobile → OTP → verify)
       └── isFormSubmitSuccess=true → hides logo/title/T&C/social buttons
            ↓
       OTP submitted → onOtpSubmit(otp)
            ↓
       Success → parent handles redirect
```

### Consent Pattern

All submit buttons in `LoginPassword` and `LoginOtp` are gated by `isTermsAccepted`. If user clicks submit without accepting T&C, a `Tooltip` with the T&C message appears.

```jsx
// Passed down to form components
<LoginPassword
  isTermsAccepted={isTermsAccepted}
  setShowConsentTooltip={setShowConsentTooltip}
  ...
/>
<Tooltip
  message={t("resource.auth.terms_and_condition")}
  isVisible={showConsentTooltip}
  onClose={() => setShowConsentTooltip(false)}
  position="bottom"
/>
```

---

## 2. Login Password (`src/page-layouts/login/component/login-password/`)

Email + password form using `react-hook-form`.

### Features
- Email and password fields
- Forgot password link (`isForgotPassword` prop controls visibility)
- Calls `onLoginFormSubmit({ email, password })`
- Calls `onForgotPasswordClick()` for forgot password flow
- Guards submit with `isTermsAccepted`

---

## 3. Login OTP (`src/page-layouts/login/component/login-otp/`)

Two-phase form: get OTP phase → verify OTP phase.

### Phase 1 — Get OTP
- Uses `MobileNumber` component for phone input
- Calls `onLoginFormSubmit({ mobile, countryCode })` on submit
- `getOtpLoading` shows loading state on button
- On success: `setIsFormSubmitSuccess(true)` hides the input form

### Phase 2 — Verify OTP
- Shown when `isFormSubmitSuccess=true`
- Displays masked mobile number (`submittedMobile`)
- 4-digit OTP input (numeric only)
- Resend OTP with cooldown timer (`otpResendTime` countdown in seconds)
- Calls `onOtpSubmit(otp)`
- Calls `onResendOtpClick()` to resend

---

## 4. MobileNumber Component (`src/page-layouts/auth/mobile-number/`)

Reusable international phone number input. Used in login, register, profile, and checkout flows.

### Library
Uses `react-international-phone` (`PhoneInput`) + `google-libphonenumber` for validation.

### Props

| Prop | Default | Purpose |
|------|---------|---------|
| `mobile` | `""` | Phone number without country code |
| `countryCode` | `"91"` | Dial code (no `+`) |
| `countryIso` | — | ISO2 code (e.g. `"in"`) to set country selector |
| `allowDropdown` | `true` | Show/hide country selector dropdown |
| `isFocused` | `false` | Auto-focus input on mount |
| `disable` | `false` | Disable entire input |
| `error` | — | `{ message: string }` — shows error text below |
| `onChange` | — | `({ mobile, countryCode, isValidNumber }) => void` |

### Validation

```js
// Returns via onChange callback
{
  mobile: "9876543210",        // digits only, no dial code
  countryCode: "91",           // dial code without +
  isValidNumber: true          // google-libphonenumber result
}
```

### Theming

Uses CSS variables from the design token system:
- `--textBody` for text color
- `--pageBackground` for background
- `--dividerStokes` for border
- `--errorText` for error state border

---

## 5. VerifyBoth (`src/page-layouts/auth/verify-both/`)

Used during registration when both mobile OTP and email OTP verification is required simultaneously.

### Sub-components
- `VerifyMobile` — 4-digit OTP form for mobile
- `VerifyEmail` — 4-digit OTP form for email

### Props

| Prop | Purpose |
|------|---------|
| `isShowMobileOtp` | Show mobile OTP panel |
| `isShowEmailOtp` | Show email OTP panel |
| `submittedMobile` | Masked mobile to display (e.g. `+91 98*****10`) |
| `mobileOtpResendTime` | Seconds remaining before resend enabled |
| `mobileFormError` | `{ message }` error to show under mobile OTP |
| `submittedEmail` | Email address displayed |
| `emailOtpResendTime` | Seconds remaining before resend enabled |
| `emailFormError` | Error for email OTP |
| `onVerifyMobileSubmit` | `({ otp }) => void` |
| `onResendMobileOtpClick` | Trigger resend |
| `onVerifyEmailSubmit` | `({ otp }) => void` |
| `onResendEmailOtpClick` | Trigger resend |

### OTP Input Rules
- `inputMode="numeric"` + `pattern="\d*"` for mobile keyboards
- `maxLength={4}`, numeric-only enforcement via `onInput`
- `dir="ltr"` always (even in RTL layouts — uses `ForcedLtr` wrapper)
- Resend button disabled when countdown > 0

---

## 6. LoginRegisterToggle (`src/page-layouts/auth/login-register-toggle/`)

Simple button to navigate from login to registration. Used at the bottom of the login page.

```jsx
<LoginRegisterToggle
  label={registerButtonLabel || t("resource.common.go_to_register")}
  onClick={onRegisterButtonClick}
/>
```

Renders: login icon + label text. `stopPropagation` + `preventDefault` on click.

---

## 7. AccountLocked (`src/page-layouts/auth/account-locked/`)

Displayed when the account is locked after too many failed attempts.

- Shows lock icon + message
- Displays support email for user to contact
- No interactive elements beyond the support email link

---

## 8. Social Login

Located in `src/page-layouts/login/component/soacial-login-button/` (legacy) and `social-login-button/` (current).

### Google
```jsx
<GoogleLoginButton
  googleClientId={googleClientId}
  onGoogleCredential={onGoogleCredential}  // receives Google JWT
  onError={handleGoogleError}
/>
```

### Facebook
```jsx
<FacebookLogin
  facebookAppId={facebookAppId}
  loginWithFacebookMutation={loginWithFacebookMutation}
  application_id={application_id}
/>
```

### Apple
```jsx
<AppleLoginButton
  appleClientId={appleId}
  onAppleCredential={onAppleCredential}
  redirectURI={appleRedirectURI}
  onError={handleGoogleError}
/>
```

Controlled by `social.google`, `social.facebook`, `social.apple` flags from the page props.

---

## 9. Auth Guard (`src/pages/`)

Attach `authGuard` as a static property on any page component to control access.

```js
// Block authenticated users from accessing login page
const loginGuard = async ({ store, router, fpi }) => {
  if (store.auth.logged_in) {
    return router.replace("/");
  }
};
LoginPage.authGuard = loginGuard;

// Require authentication for protected pages
const isLoggedIn = async ({ store, router, fpi }) => {
  if (!store.auth.logged_in) {
    const userData = await fpi.auth.fetchUserData();
    if (!userData) return router.replace("/auth/login");
  }
};
ProfilePage.authGuard = isLoggedIn;
```

---

## 10. Mode Toggle (`src/page-layouts/login/component/login-mode-button/`)

Button to switch between OTP and Password login modes.

```jsx
<LoginModeButton
  onLoginToggleClick={onLoginToggleClick}  // parent toggles isOtp/isPassword
  isOtp={isOtp}                            // current mode
/>
```

---

## Translation Keys (Auth)

```
resource.auth.login.login
resource.auth.login.login_to_shop
resource.auth.login.login_caps
resource.auth.verify_mobile
resource.auth.verify_email
resource.auth.terms_and_condition
resource.common.mobile
resource.common.go_to_register
resource.common.otp_sent_to
resource.common.enter_otp
resource.common.resend_otp
resource.common.didnt_receive_otp
resource.common.enter_valid_otp
resource.common.optional_lower
```

---

## Common Gotchas

1. **`isFormSubmitSuccess` hides the entire header/logo/T&C section** — don't add elements outside this guard if they should persist during OTP verification.
2. **`MobileNumber` onChange gives full `{ mobile, countryCode, isValidNumber }`** — don't assume it returns just the number string.
3. **OTP fields always use `dir="ltr"`** — even in RTL apps. Use `ForcedLtr` wrapper for displayed phone numbers.
4. **`countryIso` vs `countryCode`** — `countryCode` is the dial code (`"91"`), `countryIso` is ISO2 (`"in"`). Both may be needed.
5. **Social login only renders when `social.google/facebook/apple` flags are truthy** — these come from the app config.
6. **T&C consent is a local state gate** — the parent page does not own `isTermsAccepted`; it's internal to the Login component. Forms call `setShowConsentTooltip(true)` if not accepted.
