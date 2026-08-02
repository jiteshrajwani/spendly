# Spec: Login and Logout

## Overview
This step implements session-based authentication for Spendly: logging in with an email/password against the `users` table, and logging out. It wires up the existing `/login` route and `login.html` form to verify credentials and start a Flask session, and replaces the `/logout` placeholder with logic that clears the session. This is the first step that introduces the concept of a "logged-in user" to the app — later steps (profile, expense CRUD) will depend on this session state to know which user is acting.

## Depends on
- Step 1 — Database Setup (`database/db.py` with `get_db()`, `users` table) — already complete.
- Step 2 — Registration (`POST /register` creates hashed-password users) — already complete.

## Routes
- `GET /login` — render the login form — public (already exists, unchanged)
- `POST /login` — validate credentials, start session, redirect on success — public (new logic added to the existing `login` view)
- `GET /logout` — clear the session, redirect to landing — logged-in (replaces the placeholder string currently returned)

## Database changes
No database changes. The `users` table (`id`, `name`, `email`, `password_hash`, `created_at`) already supports login as-is. Login will `SELECT` from this existing table using a parameterized query.

## Templates
- **Create:** none
- **Modify:** `templates/login.html` — no structural changes expected; it already posts `email`, `password` to `/login` and already renders `{{ error }}` when passed.
- **Modify:** `templates/base.html` — nav currently always shows "Sign in" / "Get started" links. Update the nav to conditionally show "Logout" (and optionally "Profile") when a user is logged in, based on session state.

## Files to change
- `app.py` — add `SECRET_KEY` config (required for Flask sessions), add `POST` handling to the `login` view (look up user by email, verify password with `check_password_hash`, store `user_id` in `session` on success, redirect to landing on success or re-render `login.html` with an `error` on failure), replace the `logout` placeholder with logic that calls `session.clear()` and redirects to `landing`.
- `templates/base.html` — conditionally render nav links based on `session.get("user_id")`.

## Files to create
None.

## New dependencies
No new dependencies. Use `flask.session` (built into Flask) and `werkzeug.security.check_password_hash` (already used via `generate_password_hash` in `database/db.py`).

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords verified with werkzeug (`check_password_hash`) — never compare plaintext
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Validate required fields (`email`, `password`) are present and non-empty server-side, not just via HTML `required`
- Use a single generic error message for both "no such user" and "wrong password" (e.g. "Invalid email or password") to avoid leaking which emails are registered
- Store only `user_id` in the session — do not store the password hash or other sensitive fields
- `app.secret_key` must be set for `session` to work; use a fixed dev value with a comment noting it should come from an environment variable in production
- `/logout` must work regardless of whether a session currently exists (no error if already logged out)
- Use `url_for()` for every internal link — never hardcode URLs

## Definition of done
- [ ] Submitting the login form with the seeded demo user (`demo@spendly.com` / `demo123`) logs in successfully and redirects to the landing page
- [ ] After successful login, `session` contains the user's `id`
- [ ] Submitting the login form with a wrong password re-renders `login.html` with a generic "Invalid email or password" error and does not start a session
- [ ] Submitting the login form with a non-existent email re-renders `login.html` with the same generic error
- [ ] Submitting with a missing field re-renders `login.html` with an error message and does not hit the database
- [ ] Visiting `/logout` while logged in clears the session and redirects to the landing page
- [ ] Visiting `/logout` while not logged in does not error and redirects to the landing page
- [ ] Nav bar shows "Logout" instead of "Sign in"/"Get started" when a user is logged in
- [ ] `GET /login` still renders the form as before
- [ ] App starts without errors and existing routes are unaffected
