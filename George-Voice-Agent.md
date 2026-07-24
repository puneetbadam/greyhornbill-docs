# George — the Voice Agent

George is Grey Hornbill's AI voice agent: golfers call in and book a tee time by talking, the same way they would with a human at the pro shop. It's built on **Vapi**, using Claude Haiku as the underlying model, Deepgram for speech-to-text, and the Elliot voice for text-to-speech.

## Two separate concerns

George's behavior is split across two places that **do not update together**:

| | Lives in | Updated by |
|---|---|---|
| Tool implementations, prompt source | Repo code (`app/`) | Git push → Railway auto-deploy |
| Tool schemas George actually calls, the live system prompt, silence/timeout settings | **Vapi dashboard** | Manual change in Vapi, directly |

A code push alone does **not** change what George does on a live call. This trips people up, so it gets called out explicitly every time a change touches George.

**Currently pending on the Vapi side** (repo code is already shipped for these):
- `check_group_availability` and `book_group_tee_times` tool schemas
- Updated system prompt reflecting the new group-booking flow
- A fix for George skipping the caller's name based on call history — he should always ask
- A fix for silent hard disconnects (`silence-timed-out`) on long calls with no goodbye — this is a Vapi config issue, not a code one

## Cost economics

Benchmarks from June 2026 (17 calls): **~140 seconds/call**, **~$0.21/call**, **~$0.091/minute**.

Pricing tiers:

| Tier | Price |
|---|---|
| Starter | $349/mo |
| Standard | $549/mo |
| Premium | $799/mo |

Margin holds up to roughly 100 calls/day. Beyond that — 150+ calls/day on the Premium tier — needs an overage clause, roughly $0.35/call above 3,000 calls/month.

## On the horizon

- **Per-course entitlement model:** `voice_enabled` × `payments_enabled` as a plan lookup table rather than scattered string conditionals in code. Designed, not yet built.
