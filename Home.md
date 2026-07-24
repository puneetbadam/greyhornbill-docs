<p align="center">
  <img src="images/greyhornbill-logo.png" alt="Grey Hornbill" width="220"/>
</p>

<h1 align="center">Grey Hornbill</h1>
<p align="center"><i>Multi-tenant golf course booking, with an AI voice agent that actually books tee times.</i></p>

<p align="center">
  🌐 <a href="https://greyhornbill.com">greyhornbill.com</a> &nbsp;|&nbsp;
  🏗️ Built by <a href="https://plusbytes.com">Plus Bytes</a>
</p>

---

## What this is

Grey Hornbill is a SaaS platform for golf courses to manage tee-time bookings across multiple channels — staff dashboard, embeddable web widget, and **George**, an AI voice agent (built on Vapi) that golfers can call to book a tee time like they would a human pro-shop attendant.

The product is also its own demo environment: **Grey Hornbill Golf Club** is a real course running on the platform, shown transparently to prospects during sales conversations.

## Wiki contents

| Page | What's in it |
|---|---|
| **[Architecture](Architecture)** | Domains, stack, repo layout, how the pieces fit together |
| **[Booking Engine](Booking-Engine)** | The single source of truth for availability & bookings — group booking, capacity guards, closures |
| **[George (Voice Agent)](George-Voice-Agent)** | Vapi setup, cost economics, what's code vs. what's Vapi-dashboard-only |
| **[Roadmap](Roadmap)** | What's queued next, and what's on the horizon |
| **[Changelog](Changelog)** | What's shipped, batch by batch |
| **[Business Context](Business-Context)** | Plus Bytes, Grey Hornbill, and how this relates to other work in the same space |

## Quick facts

- **Domains:** marketing site at `greyhornbill.com`, admin/staff app at `app.greyhornbill.com`, API at `api.greyhornbill.com`
- **Stack:** FastAPI + SQLite (Railway), single-file admin dashboard, embeddable widget, Vapi voice agent
- **Core principle:** every booking channel — George, the widget, and the staff dashboard — shares one booking engine. No forked logic, anywhere.

---
<sub>This wiki documents the product for prospects, collaborators, and future-me. For the live product, visit **[greyhornbill.com](https://greyhornbill.com)**. For the company behind it, visit **[plusbytes.com](https://plusbytes.com)**.</sub>
