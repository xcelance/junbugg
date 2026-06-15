# JunBugg — Member & Stripe Features Guide

> **For:** Site owners & administrators  
> **Last updated:** June 2026

---

## 📖 Table of Contents

1. [What This Guide Covers](#1-what-this-guide-covers)
2. [How Payments Work on JunBugg](#2-how-payments-work-on-junbugg)
3. [Stripe Account Setup (Admin)](#3-stripe-account-setup-admin)
4. [Setting Up Membership Plans](#4-setting-up-membership-plans)
5. [Donation Feature](#5-donation-feature)
6. [Artist Stripe Connect (Getting Paid)](#6-artist-stripe-connect-getting-paid)
7. [How Money Splits (Platform Fee)](#7-how-money-splits-platform-fee)
8. [Admin Dashboard — Donations Card](#8-admin-dashboard--donations-card)
9. [Testing Payments](#9-testing-payments)
10. [Editing Text on the Donation Pages](#10-editing-text-on-the-donation-pages)
11. [Troubleshooting](#11-troubleshooting)

---

## 1. What This Guide Covers

This guide explains all the payment and donation features installed on the site:

| Feature | What It Does |
|---------|-------------|
| **Membership Pro** | Manages membership/subscription plans (fan clubs, paid events, etc.) |
| **Stripe Checkout** | The payment method members use to pay (credit/debit card) |
| **Stripe Connect** | Allows Artists to receive payouts directly to their bank account |
| **Donation System** | Lets fans donate to Artists through their profile pages |
| **Admin Donation Dashboard** | Shows donation stats and history in the admin panel |

---

## 2. How Payments Work on JunBugg

```
Fan/User                    JunBugg Site                    Stripe (Payment Processor)
    |                            |                                  |
    |  Clicks "Subscribe" or     |                                  |
    |  "Donate"                  |                                  |
    |--------------------------->|                                  |
    |                            |  Creates Stripe Checkout Session |
    |                            |--------------------------------->|
    |                            |                                  |
    |  Redirected to Stripe      |                                  |
    |  secure payment page       |                                  |
    |<---------------------------|                                  |
    |                            |                                  |
    |  Enters card details       |                                  |
    |-------------------------------------------------------------->|
    |                            |                                  |
    |  Payment successful        |                                  |
    |<--------------------------------------------------------------|
    |                            |                                  |
    |  Redirected back to        |                                  |
    |  JunBugg                   |                                  |
    |<---------------------------|                                  |
    |                            |                                  |
    |  Membership activated      |                                  |
    |  or Donation recorded      |                                  |
    |                            |                                  |
```

**No credit card data ever touches the JunBugg server.** Stripe handles all sensitive payment details securely.

---

## 3. Stripe Account Setup (Admin)

Before any payments work, you need a **Stripe account** and the API keys configured.

### Step 1: Create a Stripe Account

1. Go to [https://dashboard.stripe.com/register](https://dashboard.stripe.com/register)
2. Sign up with your business email
3. Complete the basic business details

### Step 2: Get Your API Keys

1. In Stripe Dashboard, go to **Developers → API Keys**
2. You will see two sets of keys:

| Key Type | Looks Like | When To Use |
|----------|-----------|-------------|
| **Test keys** (starts with `pk_test_` / `sk_test_`) | `pk_test_51ABC...` | Development & testing |
| **Live keys** (starts with `pk_live_` / `sk_live_`) | `pk_live_51XYZ...` | When site goes live |

### Step 3: Enter Keys in Joomla Admin

1. Go to **Membership Pro → Plugins**
2. Find and open **Stripe Checkout** (`os_stripe`)
3. Set **Mode** to `Test` for now
4. Paste the keys:

| Setting | Value |
|---------|-------|
| Test Publishable Key | `pk_test_...` |
| Test Secret Key | `sk_test_...` |
| Mode | `Test` |
| Currency | `USD` |

5. **Save & Close**

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Plugins → Stripe Checkout
> **What to capture:** The Stripe plugin settings/config page showing: / Mode dropdown set to "Test" / Test Publishable Key field filled in / Test Secret Key field filled in / Currency field / Payment Processing Fee section
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 4. Setting Up Membership Plans

Membership Pro is used to create subscription plans for:

- **Fan Clubs** — Monthly recurring membership to support an Artist
- **Paid Events** — One-time payment for access to events/listening parties
- **Donations** — Special plans marked as "donation" for receiving tips

### Create a Plan

1. Go to **Membership Pro → Plans → New**
2. Fill in the details:

| Field | Example |
|-------|---------|
| Title | `Fan Club Monthly — Artist Name` |
| Price | `5.00` |
| Duration | `1 Month` |
| Recurring | `Yes` (for fan clubs) or `No` (for events/donations) |

3. **Save**

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Plans → New
> **What to capture:** The plan creation form showing: / Title field / Price field / Duration / Recurring settings
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### Setting Up a Donation Plan

1. Create a plan as above with **Recurring = No**
2. In the **Parameters** / **Custom Fields** section, add:
   - `artist_donation` = `1`
   - `artist_user_id` = the Artist's Joomla user ID
3. Save

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Plans → [Edit Plan] → Parameters tab
> **What to capture:** The plan parameters section showing: / `artist_donation` field set to `1` / `artist_user_id` field with a user ID
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 5. Donation Feature

Fans can donate to Artists directly from the Artist's profile page.

### How Donations Work

1. Fan visits an Artist's profile page
2. They see a **Donate** section with preset amounts ($5, $10, $25, $50) or a custom amount
3. Fan chooses an amount and clicks **Donate**
4. They are taken to Stripe Checkout to enter card details
5. After successful payment, the Artist receives an email notification
6. The donation is recorded in the database

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Frontend — Artist Profile page
> **What to capture:** The Donate section showing: / Preset amount buttons ($5, $10, $25, $50) / Custom amount input field / Donate button
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### Where Donations Are Recorded

All donations are stored in the database and can be viewed in the admin **Donations** menu item under **Membership Pro**.

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Donations
> **What to capture:** The donations list/table showing: / Date, Artist name, Donor name, Amount, Status columns / At least one completed donation row
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 6. Artist Stripe Connect (Getting Paid)

Artists need to connect their own Stripe account to receive payouts.

### How an Artist Connects Stripe

1. Artist logs in to the site
2. Goes to their **Dashboard** (in their profile)
3. In the **Payout Settings** section, clicks **Connect with Stripe**
4. A Stripe Express onboarding window opens
5. Artist fills in:
   - Personal details (name, address, etc.)
   - Bank account info for payouts
6. Once approved by Stripe, the artist can receive payouts

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Frontend — Artist Dashboard → Payout Settings
> **What to capture:** The Stripe Connect section showing: / "Connect with Stripe" button / Status information (if already connected)
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### Artist Dashboard — What Artists See

After connecting Stripe, the Artist Dashboard shows:

| Section | What It Shows |
|---------|---------------|
| **Payout Status** | Whether Stripe account is active, pending requirements |
| **Donations** | Total donations received, toggle to enable/disable donations |
| **Fan Clubs** | Manage fan club membership plans |
| **Paid Events** | Create and manage paid events |

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Frontend — Artist Dashboard (full page)
> **What to capture:** The complete artist dashboard showing: / Payout Status section / Donations section / Fan Clubs section / Paid Events section
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 7. How Money Splits (Platform Fee)

When a fan pays for something, the money is split automatically:

```
Fan pays $10
       │
       ▼
  Stripe Processes Payment (~2.9% + $0.30 fee)
       │
       ▼
  ┌─────────────────────────────────────┐
  │                                     │
  │  80% → Artist's Stripe Account      │
  │         ($7.76 after Stripe fees)   │
  │                                     │
  │  20% → JunBugg Platform Fee         │
  │         ($1.94)                     │
  │                                     │
  └─────────────────────────────────────┘
```

- **Artists** get paid directly to their connected Stripe account
- **JunBugg** keeps a platform fee (configurable percentage)
- Money moves automatically — no manual payout needed

### Adjust Platform Fee

1. Go to **Membership Pro → Plugins → Stripe Checkout**
2. Find **Payment Processing Fee (%)**
3. Default is `20` (20%)
4. Change to any percentage you want
5. Save

---

## 8. Admin Dashboard — Donations Card

When you visit **Membership Pro → Dashboard** in the admin area, you will see a **Donations** card showing:

- **Total donations amount** across all artists
- **Number of donations** completed
- **Recent donations table** with Date, Artist, Donor, Amount, Status

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Dashboard
> **What to capture:** The Donations card/widget showing: / Total amount badge / Donation count badge / Recent donations table with rows
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 9. Testing Payments

Before going live, test the payment flow using Stripe test mode.

### Test Credit Card

Use this card number in Stripe Checkout:

```
Card number:  4242 4242 4242 4242
Expiry:       Any future date (e.g. 12/28)
CVC:          Any 3 digits
```

### Test Flow

1. Set the Stripe plugin to **Test** mode
2. As a fan, go to an Artist profile and click **Donate**
3. Complete the Stripe Checkout using the test card
4. You should be returned to the site with a success message
5. Check the **Donations** admin page — the donation should show as **completed**

### Switching to Live Mode

When ready to accept real payments:

1. Go to **Membership Pro → Plugins → Stripe Checkout**
2. Change **Mode** to `Live`
3. Replace test keys with live keys:

| Setting | Value |
|---------|-------|
| Live Publishable Key | `pk_live_...` |
| Live Secret Key | `sk_live_...` |

4. Save

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Plugins → Stripe Checkout (Live mode)
> **What to capture:** The Stripe plugin settings with: / Mode set to "Live" / Live Publishable Key filled in / Live Secret Key filled in
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 10. Editing Text on the Donation Pages

The text shown on donation pages (headings, buttons, descriptions) can be changed without editing code.

### How to Change Text

1. Go to **Extensions → Language Overrides → New**
2. Set:
   - **Language:** `English (en-GB)`
   - **Client:** `Administrator`
3. Click **Select** next to **Language Constant**
4. Search for `PLG_DONATIONADMIN_` to see all editable strings
5. Select the one you want to change
6. Enter the new text in **Text** field
7. **Save & Close**

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Extensions → Language Overrides → New
> **What to capture:** The Language Override form showing: / Language set to "English (en-GB)" / Client set to "Administrator" / Language Constant field with search results for PLG_DONATIONADMIN_ / Text field with a custom value entered
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### Editable Strings

| Constant | Default Text | What It Changes |
|----------|-------------|-----------------|
| `PLG_DONATIONADMIN_RENEW_SUBSCRIPTION_PAGE_TITLE` | Donation for [PLAN_TITLE] | Browser page title |
| `PLG_DONATIONADMIN_SUBSCRIION_RENEW_FORM_HEADING` | Make a Donation | Main heading on page |
| `PLG_DONATIONADMIN_PROCESS_RENEW` | Complete Donation | Submit button text |
| `PLG_DONATIONADMIN_RENREW_MEMBERSHIP` | Make a Donation | Link/button text |
| `PLG_DONATIONADMIN_RENREW_MEMBERSHIP_DESCRIPTION` | Please choose the donation option... | Description text (renew membership view) |
| `PLG_DONATIONADMIN_FORM_MESSAGE` | Please choose the donation option... | Message on the donation form |

---

## 11. Troubleshooting

### Payment Problems

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| Stripe Checkout doesn't open | API keys not entered or incorrect | Check keys in Membership Pro → Plugins → Stripe Checkout |
| "No such payment method" | Plugin not published | Go to Membership Pro → Plugins and publish `os_stripe` |
| Payment fails in test mode | Using live keys in test mode | Switch Mode to `Test` and use test keys |
| Artist not receiving payouts | Artist hasn't connected Stripe | Ask artist to go to their Dashboard and connect Stripe |
| Wrong currency | Currency setting incorrect | Set currency in Stripe plugin settings |

### Donation Problems

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| Donate button not showing | Artist hasn't enabled donations | Artist should go to Dashboard → enable Donations |
| Donation plan not found | Plan missing `artist_donation` param | Check plan parameters include `artist_donation = 1` |
| Donation card not showing in admin | Plugin not enabled | Go to **Extensions → Plugins → System → Donation Admin** and enable |

### Language Text Not Changing

If you edited a language override but the old text still shows:

1. Go to **System → Clear Cache**
2. Check **Administrator** cache
3. Click **Purge Expired**
4. Also go to **System → Clear Joomla Cache**
5. Try the page again

---

## Quick Reference — Joomla Menu Paths

| What You Need | Path in Admin |
|--------------|---------------|
| Stripe Plugin Settings | **Membership Pro → Plugins → Stripe Checkout** |
| Membership Plans | **Membership Pro → Plans** |
| Donation Records | **Membership Pro → Donations** |
| Artist Dashboard | **Community → Artist Dashboard** (on frontend) |
| Language Overrides | **Extensions → Language Overrides** |
| Plugin Manager | **Extensions → Plugins → Search "Donation Admin"** |
| Stripe Dashboard | [https://dashboard.stripe.com/](https://dashboard.stripe.com/) |

---

*For technical support, contact the development team.*
