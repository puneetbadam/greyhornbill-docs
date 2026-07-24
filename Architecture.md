# Architecture

## Domain layout

| Domain | Purpose | Hosting |
|---|---|---|
| `greyhornbill.com` | Marketing / landing site, lead capture | Hostinger (static) |
| `app.greyhornbill.com` | Course admin/staff dashboard (Admin + User roles), superadmin console via role routing | Railway |
| `api.greyhornbill.com` | FastAPI backend | Railway |

The CRM currently lives inside `app.greyhornbill.com` rather than as a separate product — it'll get split out if it grows enough to warrant it.

## Stack

- **Backend:** FastAPI + SQLite, running on a Railway volume (`/data/greyhornbill.db`)
- **Admin dashboard:** single-file `admin_dashboard.html` (intentionally monolithic — see [Booking Engine](Booking-Engine) for why this matters when editing)
- **Widget:** `widget.js`, a self-contained Shadow DOM IIFE, embeddable on any course's site
- **Voice:** Vapi, with **George** as the persona — see **[George](George-Voice-Agent)**
- **Email:** Resend HTTP API (`api.resend.com/emails`) via `httpx` — no SMTP anywhere, deliberately (Railway blocks SMTP; irrelevant here)
- **Calendar:** Google Calendar, one-way mirror per course, single shared service account
- **Auth/security:** custom JWT, PBKDF2 password hashing, Cloudflare Turnstile on all write endpoints
- **Repo:** local at `D:\GreyHornbill\greyhornbill`; Railway auto-deploys from GitHub `main`

## The one non-negotiable rule

All three booking channels — George (voice), the widget, and the staff dashboard — call the **same** engine functions: `get_available_slots()` and `create_booking()`, among others in `app/booking/engine.py`. There is no per-channel booking logic. This is the architectural spine of the system: every feature (group bookings, capacity limits, closures) is built once in the engine and every channel gets it automatically.

## Two `routes.py` files — a known collision hazard

`app/admin/routes.py` and `app/widget/routes.py` share a filename. Flat zips of changes have caused mix-ups before. Mitigation: deliveries preserve folder structure, and `findstr` sanity checks are run after extracting any batch touching both.
