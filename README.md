# Bano Qabil — Page 1: Authentication (Frontend Only)

This package contains the **frontend-only** authentication experience for
the Bano Qabil mobile app: Login, Registration, Forgot Password, and a
demo role selector. There is no backend, no Firebase, and no fake
authentication logic anywhere in this codebase.

## Running it

```bash
flutter pub get
flutter run
```

## Architecture

```
UI (pages/widgets)
      │
      ▼
AuthController (application/auth_controller.dart)
      │  holds AuthViewState per screen (idle/loading/success/failure)
      ▼
AuthRepository (repositories/auth_repository.dart)   ← interface only
      │
      ▼
[ Not implemented yet — plug in REST, Firebase, Supabase, etc. ]
```

* `AuthRepository` is an abstract contract. No class in this project
  implements it.
* `AuthController` accepts a nullable `AuthRepository`. While it's
  `null` (the current state), every sign-in/register/reset action
  resolves to an honest `AuthFailureType.notConnected` state — the UI
  never pretends a fake user was authenticated.
* `UserProfile` / `UserRole` are typed models with no hardcoded
  instances anywhere in the codebase.

## Wiring up a real backend later

1. Create a class that implements `AuthRepository` (e.g.
   `lib/repositories/rest_auth_repository.dart`), backed by whatever
   service you choose.
2. In `lib/main.dart`, change:

   ```dart
   create: (_) => AuthController(repository: null),
   ```

   to:

   ```dart
   create: (_) => AuthController(repository: YourAuthRepositoryImpl()),
   ```

No other file needs to change — the UI, controller, and routing were
built against the abstraction, not a specific backend.

## Demo Access

The "Demo Access" buttons on the login screen do **not** sign anyone
in. They only navigate to a placeholder screen (`RolePlaceholderPage`)
for each `UserRole`, so the routing structure can be exercised before
the real dashboards and real authentication exist.

## What's intentionally out of scope

* Student / Instructor / Coordinator dashboards (placeholders only)
* Any backend, API, or Firebase integration
* Persisted sessions
