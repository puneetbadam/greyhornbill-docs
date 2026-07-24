# Changelog

Dated by when work shipped. Suite counts are the full `pytest` pass count at time of delivery.

## 19 July 2026 — Large-group booking (Batches 1–3)
- `players_per_slot` per course, `group_ref` on bookings
- Engine ladder: `slots_needed`, `_distribute_party`, `get_consecutive_runs`, `book_group`
- Two new George tools registered: `check_group_availability`, `book_group_tee_times` (Vapi dashboard side still pending)
- Widget: `/widget/config` returns live `players_per_slot`; Players selector capped dynamically; "More than N players?" routes into the callback queue
- **Suite: 273 passing**

## 19 July 2026 — Slot capacity guard (GH-277886 fix, Batch 2)
- `SlotCapacityError` in the booking engine; `create_booking` fails fast on over-capacity party size, before any DB write
- All three booking entry points (staff admin, widget, George) have explicit handlers
- Shared `check_party_capacity` / `_by_course_id` helper extracted
- PATCH edit endpoint now enforces the cap too (grandfathers existing over-cap rows — blocks increases, allows reductions and time-only edits)
- Staff "New Booking" dropdown and Edit modal spinner rebuilt to read capacity dynamically
- **Suite: 282 passing**

## ~13 July 2026 — Course closures (FB-13)
- `course_closures` table, exclusion set unioned into the shared engine
- Auto-cancel of confirmed bookings inside a new closure window; affected golfers filed to the callback queue
- `SlotClosedError` guard in `create_booking`
- Staff UI: tee sheet closure marking with preview modal
- **Batch 11 follow-up:** overlap/edit logic — `closures_overlapping()`, `classify_closure_conflicts()`, atomic replace on supersede, `update_closure()` cancels only the newly-grown portion, PATCH + preview routes, frontend conflict banners, Edit buttons on tee sheet and Closures page
- **Suite: 228 passing** (at initial shipment)

## 13 July 2026 — User management
- Admin-initiated temporary password reset (hash-only storage, shown once, no email dependency)
- Force password change on first login (`must_change_password` flag)
- Soft-delete "Remove" (`is_removed=1`, preserves history); hard delete kept as a superadmin-only escape hatch
- **Suite: 188 passing**

## 17 July 2026 — Marketing site / lead capture
- Qualified lead capture modal, replacing Calendly CTAs
- `POST /public/lead` with Turnstile + honeypot protection
- `leads` table; Resend notification to `hello@greyhornbill.com`
- CORS extended to `greyhornbill.com`
- **Suite: 254 passing**

## Ongoing — Email
All transactional email (forgot-password, welcome/set-password, booking/reschedule/cancel confirmations, support tickets) runs on the Resend HTTP API via plain HTTPS POST. No SMTP anywhere in the stack.
