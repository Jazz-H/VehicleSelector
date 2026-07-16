# Motorpool — Accounts & Settings Scope

**Direction:** local-first **profiles** (no login required) **+ optional Supabase sign-in for cloud sync**.
The app keeps working fully offline as a template; signing in is purely additive (syncs a profile across devices).

Status: **scoping / not started.** Phase 1 is buildable + testable today; Phases 2–3 need a Supabase project.

---

## 1. Principles
- **Local-first.** The app always reads/writes local storage first; it never *requires* an account to work.
- **Additive auth.** Sign-in only enables sync. Sign-out keeps all local data intact.
- **Privacy by default.** Nothing leaves the device until a user explicitly signs in.
- **Non-destructive migration.** Existing `mp.*` data becomes the first profile automatically.

---

## 2. Data model

### Local (localStorage)
- `mp.profiles` → `[{ id, name, avatar?, createdAt, cloud?: { userId, lastSynced } }]`
- `mp.activeProfile` → `"<id>"`
- **Per-profile namespacing:** every existing key `mp.<x>` becomes `mp.p.<profileId>.<x>`.
  (e.g. `mp.mbud.cap` → `mp.p.p1.mbud.cap`). A tiny storage shim maps get/set through the active profile so the rest of the app is untouched.
- Global (not per-profile): `mp.profiles`, `mp.activeProfile`, `mp.prefs.*`, `mp.session`.

### Cloud (Supabase / Postgres) — Phases 2–3
- `auth.users` — managed by Supabase Auth.
- `profiles` table: `id (uuid)`, `user_id (uuid, fk)`, `name`, `data (jsonb)`, `updated_at`.
  - `data` is the same JSON blob the current **Export** produces (the `mp.*` map for that profile) — so sync reuses the export/import format we already have.
- **Row-Level Security:** `user_id = auth.uid()` on all reads/writes. The client only ever holds the public **anon key** (safe under RLS).

---

## 3. Auth flow (Supabase, Phase 2)
- **Sign in:** email magic-link **or** password, plus OAuth (**Google** and/or **GitHub**). _(final list = open decision)_
- **Sign out:** clear Supabase session + `mp.session`; local profiles/data remain.
- **Session:** Supabase JS SDK persists the session; app checks it on load and shows Account state.
- Client config: `SUPABASE_URL` + `SUPABASE_ANON_KEY` inlined in the page (public, RLS-protected).

---

## 4. Sync strategy (Phase 3)
- **Local-first write-through:** UI writes local immediately; when signed in, a debounced push serializes the active profile's `mp.*` blob → `profiles.data`.
- **Pull:** on sign-in and on load, fetch the user's profiles; offer to import any not present locally.
- **Conflict:** start with **last-write-wins** using `updated_at`; if both local and cloud changed since `lastSynced`, show a "Keep local / Keep cloud" prompt. (No field-level merge in v1.)
- **Offline:** fully functional; queue a sync for when back online + signed in.
- **Controls:** show *Last synced*, a manual **Sync now**, and an **auto-sync** toggle.

---

## 5. Settings page redesign (applies across phases)
Restructure the single "Your Data" screen into labeled sections:
- **Account** — signed-out: "Sign in to sync"; signed-in: email/name + **Sign out**.
- **Profiles** — list with active indicator; **switch / add / rename / delete**; avatar (initial or emoji).
- **Sync** — status, last-synced, **Sync now**, auto-sync toggle _(visible when signed in)_.
- **Preferences** — theme, default landing tab, units ($/mi). _(nice-to-have)_
- **Data** — existing **Export / Import / Reset**, now scoped to the active profile.
- **Danger zone** — delete profile; delete account + all cloud data.

---

## 6. Migration (first load after update)
1. If `mp.profiles` is absent, create profile `p1` "My Build".
2. Move every existing `mp.<x>` (except globals) → `mp.p.p1.<x>`.
3. Set `mp.activeProfile = p1`. Set a `mp.migrated.profiles` flag so it runs once.
Non-destructive, reversible via the pre-existing backup export.

---

## 7. Phased build plan
- **Phase 1 — Local profiles (Path A). Fully static, testable here.**
  Storage shim + namespacing + migration; Settings **Profiles** section (switch/add/rename/delete); per-profile export/reset. Ships standalone value with zero backend.
- **Phase 2 — Supabase auth (part of Path C). Needs Supabase project + your live verification.**
  Add SDK + config; **Account** section; sign in / sign out (email + one OAuth); session on load. Auth only, no sync yet.
- **Phase 3 — Cloud sync (completes Path C).**
  `profiles` table + RLS; push/pull; last-synced; Sync now + auto-sync; conflict prompt.
- **Phase 4 — Polish.**
  More OAuth providers, avatars, preferences, delete-account, empty/onboarding states.

---

## 8. Constraints & what I need from you
- **Supabase project (free tier)** — you create it (or authorize me to scaffold and you paste keys). I need the **project URL** + **anon key** (both safe to commit under RLS).
- **Login methods** — email magic-link vs. password, and which OAuth (Google / GitHub / none) for v1.
- **Sync scope** — sync **all** local profiles to your account, or **one profile per account**?
- **Live testing** — this sandbox is network-restricted, so I can't complete real OAuth/DB round-trips here. I build + you verify the live auth/sync; Phase 1 is fully verifiable in-repo.

---

## 9. Open decisions (to finalize before Phase 2)
1. OAuth providers for v1.
2. Sync scope (all profiles vs one).
3. Conflict UX (auto last-write-wins vs. always prompt).
4. Where the Supabase keys live (inline in `index.html` vs. a small `config.js`).
