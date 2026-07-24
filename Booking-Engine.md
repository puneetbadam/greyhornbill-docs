# Booking Engine

`app/booking/engine.py` is the single source of truth for tee-time availability and booking creation. Every channel — George, the widget, the staff modal — calls into it. Google Calendar is a **non-blocking, post-commit mirror** (`mirror.py`): it reflects bookings outward, it never gates them.

Per-course, admin-editable settings live as DB columns: `tee_interval_minutes`, `open_hour`, `close_hour`, `players_per_slot`.

## Large-group booking

A golfer booking more than one slot's worth of players needs a "group" of tee times filled sensibly, not just one arbitrary slot. The engine's ladder, in priority order:

1. **Consecutive slots** — fill the tightest run of adjacent tee times first
2. **Near-consecutive, tightest fit** — if a perfect consecutive run isn't available
3. **Up to 2 gap-slots** — as a last resort before giving up

Key pieces: `players_per_slot` (per course), `group_ref` (links the N bookings that make up one group), and engine functions `slots_needed`, `_distribute_party`, `get_consecutive_runs`, `book_group`.

**Design choice:** a group is stored as **N linked rows sharing a `group_ref`**, not one spanning row. This keeps the blast radius small when a golfer cancels, reschedules, or (later) needs a Stripe refund on part of a group.

George has two voice tools for this: `check_group_availability` and `book_group_tee_times`. These fixed a latent bug in the old flow, where George called `book_tee_time` once per slot with no transaction wrapping and no group linkage — a partial failure could leave a golfer with some but not all of their requested slots, invisibly.

## Capacity guard

`players_per_slot` is an absolute per-slot cap. `create_booking` fails fast — before any DB write — if party size exceeds it (`SlotCapacityError`). All three entry points (staff admin, widget, George) handle this explicitly. The same check is shared via `check_party_capacity` / `_by_course_id` rather than duplicated.

The edit (PATCH) path enforces the same cap, with one locked exception: **existing over-cap bookings are grandfathered** — edits can reduce party size or change time, but can't increase party size back up past the cap. This closed a real bug (GH-277886) where the create-path guard existed but the edit path didn't, letting staff re-open the same hole through "Edit."

## Closures

`course_closures` defines blackout windows per course. The engine excludes them from availability (`_closed_tee_datetimes`), auto-cancels any confirmed bookings that fall inside a newly created closure, and files affected golfers into a callback queue for staff follow-up. Overlapping or edited closures go through explicit conflict classification (`closures_overlapping`, `classify_closure_conflicts`) rather than a binary check, because different overlap types (blocked / replace / overlap / clear) need different UI treatment. Editing a closure only cancels the *newly grown* portion of the window, not bookings that were already safely inside the original window.

## Principles that came out of building this

- **Capacity changes are forward-only.** Changing `players_per_slot` governs new bookings; it never retroactively re-plans bookings already committed, in either direction.
- **"Consecutive" is course-relative** — adjacency is measured in the course's own `tee_interval_minutes`, not a fixed number of minutes.
- **Display bugs and engine bugs get diagnosed separately.** A closure once looked wrong in the browser (timezone rendering showed IST as if it were CDT) while the engine itself was blocking the correct hours the whole time.
- **Party size before slot selection.** Letting someone pick a tee time and then discarding that choice once they enter party size is a UX bug — party size must be asked first.
