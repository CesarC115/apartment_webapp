# PropFlow — Apartment Management Platform
### Product Plan & Build Roadmap

---

## Product Concept

A mobile-first web app landlords pay a monthly subscription for. Each landlord gets their own branded portal (e.g. `landlordname.propflow.app`). Tenants log in to pay rent, submit maintenance requests, and view lease info. You host it, you own the recurring revenue.

**Key characteristics:**
- Mobile-first Progressive Web App (PWA)
- Multi-tenant SaaS architecture
- Stripe Connect for payment routing
- AI-powered maintenance triage via Anthropic API
- Landlord white-label portal

---

## Target Customer

**Who you sell to:** Individual landlords (small-to-mid portfolio, 1–50 units)
**Their pain:** Rent collected via Venmo/check, maintenance tracked in texts, zero visibility into their portfolio
**Your pitch:** "One app your tenants actually use — rent, maintenance, and lease info — all for less than a property management company charges per hour"

---

## Two User Roles

### Landlord (pays you)
- Dashboard showing rent collection status across all units
- Add/edit properties, units, and tenants
- View and manage all maintenance tickets
- Receive Stripe payouts automatically
- Upload and store lease documents
- AI-generated maintenance summaries and urgency triage

### Tenant (uses the app)
- Pay rent via credit card or ACH bank transfer
- View full payment history and receipts
- Submit maintenance requests with photo uploads
- Track request status (open → in progress → resolved)
- Receive email/push notifications on updates

---

## Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Frontend | Next.js 14 + TypeScript | App Router, server components, PWA support |
| Styling | Tailwind CSS | Fast mobile UI, utility-first |
| Backend / DB | Supabase | Postgres + auth + storage + realtime |
| Payments | Stripe Connect | Handles landlord payouts automatically |
| AI features | Anthropic API (Claude) | Maintenance triage, summaries, tenant comms |
| Hosting | Vercel | Zero-config deploys, free tier to start |
| Email | Resend | Simple API, generous free tier |
| Push notifications | Expo (optional phase 2) | If native app is needed later |

---

## Architecture Notes

### Multi-tenant isolation
Each landlord's data must be completely isolated. Use **Supabase Row Level Security (RLS)** policies — every table gets a `landlord_id` column and RLS rules that ensure queries only return rows belonging to the authenticated landlord. Tenants are scoped similarly to their unit/property.

### Stripe Connect
Use **Stripe Connect Express** (not regular Stripe). Money flows: Tenant card → Stripe → Landlord's connected account. You take a platform fee per transaction (e.g. 1% or flat $1/transaction) on top of the monthly subscription. This must be designed correctly from day one — retrofitting it later means rewriting the entire payment layer.

### AI differentiation
When a tenant submits a maintenance request, Claude automatically:
1. Classifies urgency (routine / urgent / emergency)
2. Suggests next action for the landlord (e.g. "Call a licensed plumber")
3. Drafts a tenant acknowledgment reply
4. Tags the category (plumbing, electrical, HVAC, etc.)

No competitor at the $29/month price point does this.

---

## MVP Build Order (8 Weeks)

### Week 1–2: Auth + multi-tenant setup
- Next.js project scaffold with TypeScript
- Supabase project + auth (email/password + magic link)
- Landlord signup flow → auto-provision their subdomain
- RLS policies for data isolation
- Basic landlord dashboard shell

### Week 2–3: Unit & tenant management
- Property and unit CRUD (create, read, update, delete)
- Tenant invite flow (landlord enters email → tenant gets invite link)
- Tenant onboarding (accept invite → set password → view their unit)
- Lease document upload and storage (Supabase Storage)

### Week 3–4: Rent payment flow
- Stripe Connect onboarding for landlords
- Tenant payment page (card + ACH via Stripe)
- Webhook handler for payment events
- Payment history views for both roles
- Automatic receipt emails via Resend

### Week 5–6: Maintenance requests
- Tenant submission form with photo upload
- Supabase Storage for images
- Ticket dashboard for landlords (list, filter by status/unit)
- Status updates with realtime sync
- Email notifications on status change

### Week 6–7: AI triage (Anthropic API)
- On new ticket creation: call Claude with ticket description + photos
- Parse structured JSON response: urgency, category, suggested action, draft reply
- Display AI summary on landlord ticket view
- One-click "send AI reply to tenant" button

### Week 7–8: Landlord sales page
- Marketing landing page (your product's public homepage)
- Feature overview, pricing table, testimonials placeholder
- Demo video embed or interactive demo
- Stripe Checkout for subscription sign-up
- Onboarding checklist for new landlords

---

## Database Schema (Key Tables)

```
landlords         id, email, name, stripe_account_id, subdomain, plan, created_at
properties        id, landlord_id, address, name
units             id, property_id, landlord_id, unit_number, monthly_rent
tenants           id, user_id, unit_id, landlord_id, lease_start, lease_end
payments          id, tenant_id, landlord_id, amount, stripe_payment_id, status, paid_at
maintenance       id, tenant_id, unit_id, landlord_id, description, photo_urls,
                  status, urgency, ai_category, ai_summary, ai_reply_draft, created_at
```

---

## Pricing Model

| Plan | Price | Units | Notes |
|---|---|---|---|
| Starter | $29/month | Up to 5 units | Perfect for first landlord customers |
| Growth | $59/month | Up to 20 units | Most common tier |
| Portfolio | $99/month | Unlimited | Large landlords, high LTV |
| Setup fee | $199 one-time | — | Onboarding + custom configuration |

**Revenue math:** 10 landlords on Growth = $590 MRR. 50 landlords = $2,950 MRR. Low churn (landlords don't switch tools easily once tenants are onboarded).

---

## Immediate Next Steps

1. **Design the database schema** — model all entities before writing app code (like designing your memory map before firmware)
2. **Scaffold the Next.js project** — `npx create-next-app@latest propflow --typescript --tailwind --app`
3. **Set up Supabase** — create project, configure auth, write initial RLS policies
4. **Build auth flow** — landlord signup → dashboard, tenant invite → onboarding

---

## Competitive Positioning

| Competitor | Price | AI | Mobile-first | Your advantage |
|---|---|---|---|---|
| Buildium | $58–$340/month | No | No | Cheaper + AI + better mobile |
| Rentec Direct | $45–$80/month | No | Partial | AI triage differentiator |
| Cozy (free) | Free | No | Partial | AI + maintenance tracking |
| **PropFlow** | **$29–$99/month** | **Yes** | **Yes** | — |

---

*Generated with Claude · PropFlow Product Plan v1.0*
