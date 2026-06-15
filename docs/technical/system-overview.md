# Technical Reference — System Overview

> **For:** Developers  
> **Covers:** Architecture, components, data flow

---

## 1. System Architecture

```
┌─────────────────────────────────────────────────────────┐
│                   Joomla CMS (4/5)                      │
│                                                         │
│  ┌─────────────────────┐  ┌──────────────────────────┐  │
│  │  Membership Pro      │  │  ArtistDashboard Plugin  │  │
│  │  (com_osmembership)  │  │  (plugins/community/)    │  │
│  │                      │  │                          │  │
│  │  ┌───────────────┐   │  │  ┌────────────────────┐  │  │
│  │  │ os_stripe     │   │  │  │ Stripe Connect     │  │  │
│  │  │ Payment Plugin│   │  │  │ Onboarding         │  │  │
│  │  └───────────────┘   │  │  └────────────────────┘  │  │
│  │                      │  │  ┌────────────────────┐  │  │
│  │  ┌───────────────┐   │  │  │ Donate Flow        │  │  │
│  │  │ MPF Framework  │   │  │  │ (Stripe Checkout)  │  │  │
│  │  └───────────────┘   │  │  └────────────────────┘  │  │
│  └─────────────────────┘  │  ┌────────────────────┐  │  │
│                           │  │ Fan Club Mgmt      │  │  │
│  ┌─────────────────────┐  │  └────────────────────┘  │  │
│  │  DonationAdmin       │  │  ┌────────────────────┐  │  │
│  │  System Plugin       │  │  │ Paid Events Mgmt   │  │  │
│  │  (plugins/system/)   │  │  └────────────────────┘  │  │
│  │                      │  └──────────────────────────┘  │
│  │  ┌───────────────┐   │                                │
│  │  │ Dashboard Card│   │  ┌──────────────────────────┐  │
│  │  │ Lang Overrides│   │  │  Stripe REST API          │  │
│  │  │ Donation Rec  │   │  │  (api.stripe.com)         │  │
│  │  └───────────────┘   │  └──────────────────────────┘  │
│  └─────────────────────┘                                │
└─────────────────────────────────────────────────────────┘
```

---

## 2. Component Overview

### 2.1 Membership Pro (`com_osmembership`)

The core membership/subscription management component. Handles plans, subscribers, payment processing.

| Aspect | Detail |
|--------|--------|
| Type | Joomla Component |
| Location | `components/com_osmembership/` |
| Admin | `administrator/components/com_osmembership/` |
| Plugin System | Custom plugin architecture (`os_payment` base class) |
| DB Prefix | `#__osmembership_` |

### 2.2 os_stripe Payment Plugin

Custom Stripe Checkout payment plugin for Membership Pro.

| Aspect | Detail |
|--------|--------|
| Type | Membership Pro Payment Plugin |
| Location | `components/com_osmembership/plugins/os_stripe.php` |
| Base Class | `os_payment` → `MPFPayment` |
| API | Direct cURL to Stripe REST API (no SDK) |

### 2.3 DonationAdmin System Plugin

Adds donation functionality to OSMembership admin and frontend.

| Aspect | Detail |
|--------|--------|
| Type | Joomla System Plugin |
| Location | `plugins/system/donationadmin/` |
| Events | `onAfterRoute`, `onAfterRender`, `onAfterStoreSubscription` |
| DB Table | `#__artist_donations` |

### 2.4 ArtistDashboard Community Plugin

Artist-facing dashboard with Stripe Connect, donations, fan clubs, events.

| Aspect | Detail |
|--------|--------|
| Type | JomSocial Community Plugin |
| Location | `plugins/community/artistdashboard/` |
| DB Table | `#__artist_stripe_accounts` |

---

## 3. Payment Flow

### One-Time Payment (Donations & Events)

```
User → Checkout → os_stripe::processPayment()
  ├─ getArtistUserIdForCheckout() → resolve artist
  ├─ getArtistStripeAccount() → get Connect account
  ├─ Stripe API: POST /v1/checkout/sessions
  │   ├─ mode: payment
  │   ├─ line_items: price data
  │   ├─ payment_intent_data:
  │   │   ├─ application_fee_amount (platform fee)
  │   │   └─ transfer_data[destination] (artist)
  │   └─ success_url → payment_confirm
  └─ Redirect user to Stripe Checkout URL

User completes payment on Stripe
  └─ Redirected back → payment_confirm
      └─ os_stripe::verifyPayment()
          └─ Stripe API: GET /v1/checkout/sessions/{id}
              └─ Confirm status = complete
```

### Recurring Payment (Fan Clubs)

```
User → Subscribe → os_stripe::processPayment()
  ├─ Stripe API: POST /v1/checkout/sessions
  │   ├─ mode: subscription
  │   ├─ line_items: recurring price data
  │   ├─ subscription_data:
  │   │   ├─ application_fee_percent (platform fee)
  │   │   └─ transfer_data[destination] (artist)
  │   └─ success_url → recurring_payment_confirm
  └─ Redirect user to Stripe Checkout URL
```

### Direct Donation (via Artist Dashboard)

```
User → Donate on profile → createDonateStripeSession()
  ├─ Insert `#__artist_donations` record (status: pending)
  ├─ Stripe API: POST /v1/checkout/sessions
  │   ├─ mode: payment
  │   ├─ payment_intent_data:
  │   │   ├─ application_fee_amount
  │   │   └─ transfer_data[destination]
  │   └─ success_url → profile with ?donate_return=1
  └─ Redirect user to Stripe

User returns → handleDonateReturnIfNeeded()
  ├─ Stripe API: GET /v1/checkout/sessions/{id}
  ├─ Update `#__artist_donations` status
  └─ sendDonationNotification() (email to artist)
```

---

## 4. Platform Fee Calculation

```
Gross Amount:                $10.00
Stripe Fee:                 -$0.59  (2.9% + $0.30)
Subtotal:                    $9.41

Platform Commission (20%):  -$1.88  (20% of $9.41)
Artist Payout:               $7.53
```

Fee percentage is configurable in os_stripe plugin settings (`payment_fee_percent`).

---

## 5. Key Dependencies

| Dependency | Version | Purpose |
|-----------|---------|---------|
| Joomla | 4.x / 5.x | CMS |
| Membership Pro | Latest | Subscription management |
| JomSocial | 4.x | Social/community features |
| PHP | 8.0+ | Runtime |
| Stripe API | 2024-06-20 | Payment processing |

---

*For developer support, contact the development team.*
