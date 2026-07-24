# Roadmap

## Next up (priority order)

1. **Tee-sheet export closure bug** — PDF/CSV exports show closed slots as "open" and inflate the open-slot count. The on-screen tee sheet renders correctly; exports need a proper three-way booked / open / closed tally.
2. **Widget: Players-first reorder** — ask party size right after date, before branching into slot selection.
3. **Widget: group proposal cards** — show group-booking options as tappable cards instead of prose.
4. **Staff group modal** — surface the engine's ladder recommendations with a manual override; absolute per-slot cap of 4 stays, but multi-slot grouping rules are relaxed for staff-side bookings.
5. **George fixes** — always ask for caller name (never skip based on call history); fix the display bug in `_resolve_caller_name`.

## On the horizon

- **Phase 3:** SMS/WhatsApp confirmations via Twilio; Stripe payment links (with staff-released account credit rather than automatic refunds, until Stripe lands)
- **Host-based routing split** between `api.` and `app.` — low priority, cosmetic
- **Per-course plan/entitlement model** — `voice_enabled` × `payments_enabled` as a lookup table
- **Time-varying capacity** — e.g. a lower cap during a morning rush, architecturally the same shape as closures (per-course window → rule); parked as a future pass on a shared "scheduling rules" engine
- **Stage 3 frontend** (flagged, not yet built):
  - Superadmin course list should show org attribution ("Operator Name: Course Name"), not a bare course name
  - The "Callbacks" nav page currently shows every booking status, indistinguishable from the main Bookings view — needs a restricted default, a course filter for multi-course operators, and possibly a rename
