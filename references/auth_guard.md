# Auth Guard (Summary)

Source: Fynd Commerce Themes - Auth Guard.

## Purpose
- Control access to routes based on authentication state.

## Pattern
- Define an `authGuard` function that checks user state.
- Attach it as a static property: `PageComponent.authGuard = authGuard`.

## Common Guards
- `loginGuard`: redirect authenticated users away from login/register.
- `isLoggedIn`: allow only authenticated users (check store, else fetch user data).

## Notes
- Use SSR-safe navigation checks when redirecting.
- Can rely on `store.auth.logged_in` if already fetched.
- Fallback: `fpi.auth.fetchUserData()` to determine status.
