# Technical Reference — API Endpoints

> **For:** Developers  
> **Covers:** Custom API endpoints and HTTP routes

---

## 1. Overview

The system does not implement REST API endpoints directly. All API calls are made server-to-server from PHP to Stripe's REST API using cURL.

---

## 2. Frontend HTTP Routes

| Path | Method | Purpose | Handler |
|------|--------|---------|---------|
| `index.php?option=com_osmembership&view=register&plan_id=N` | GET | Donation/Subscription form | OSMembership |
| `index.php?option=com_osmembership&task=payment_confirm&session_id=X` | GET | Payment confirmation callback | `os_stripe::verifyPayment()` |
| Profile page with `?donate_return=1` | GET | Return from donation | `plgSystemDonationAdmin::handleDonateReturnIfNeeded()` |
| `index.php?artist_dashboard=N` | GET | Artist dashboard tab | `plgCommunityArtistDashboard` |

---

## 3. Admin HTTP Routes

| Path | Method | Purpose |
|------|--------|---------|
| `administrator/index.php?option=com_osmembership&view=plan&id=N` | GET | Edit plan (admin) |
| `administrator/index.php?option=com_osmembership` | GET | OSMembership dashboard |
| `administrator/index.php?option=com_plugins&view=plugin&extension[id]=N` | GET | DonationAdmin plugin config |

---

## 4. Stripe API Endpoints Used

All calls go to `https://api.stripe.com/v1/`. See [Stripe Plugin Reference](stripe-plugin.docs#5-stripe-api-calls) for details.

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/v1/checkout/sessions` | POST | Create checkout session |
| `/v1/checkout/sessions/{id}` | GET | Retrieve session status |
| `/v1/accounts` | POST | Create Connect account |
| `/v1/accounts/{id}` | GET | Retrieve account status |
| `/v1/account_links` | POST | Create onboarding link |

---

## 5. Stripe Webhooks (Future)

Currently, the system does not set up Stripe webhooks. Payment confirmations are handled via **return URL redirects** (synchronous).

If asynchronous handling is needed later, the following webhook events should be listened to:

| Event | Purpose |
|-------|---------|
| `checkout.session.completed` | Confirm payment |
| `payment_intent.succeeded` | Confirm payment intent |
| `account.updated` | Stripe Connect account status change |
| `customer.subscription.updated` | Fan club subscription changes |

To add webhooks:
1. Register a webhook endpoint in Stripe Dashboard
2. Add a handler to `os_stripe` plugin
3. Update donation/subscription records asynchronously

---

*For developer support, contact the development team.*
