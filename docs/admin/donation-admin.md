# DonationAdmin Plugin

> **For:** Site administrators  
> **Covers:** Donation system setup, dashboard card, configuration

---

## 1. What It Does

The DonationAdmin system plugin adds donation features to Membership Pro:

- **Donations Dashboard Card** — Shows donation stats in the admin panel
- **Language Overrides** — Changes Membership Pro text to donation-specific wording
- **Donation Recording** — Automatically records donations when a subscription completes
- **Custom Amount Support** — Allows fans to enter custom donation amounts

---

## 2. Installation

1. Go to **Extensions → Install Extensions**
2. Upload `donationadmin.zip`
3. Go to **Extensions → Plugins**
4. Search for `Donation Admin`
5. Open and set **Status** to **Enabled**
6. **Save & Close**

---

## 3. Admin Dashboard Card

When enabled, a **Donations** card appears on the **Membership Pro → Dashboard** page showing:

```
┌──────────────────────────────────────────┐
│  Donations (All Artists)                 │
│  [$1,234.56 total] [42 donations]        │
│                                          │
│  ┌────────┬────────┬──────┬──────┬──────┐│
│  │ Date   │ Artist │ Donor│Amount│Status││
│  ├────────┼────────┼──────┼──────┼──────┤│
│  │ ...    │ ...    │ ...  │ ...  │ ...  ││
│  └────────┴────────┴──────┴──────┴──────┘│
└──────────────────────────────────────────┘
```

### What's Tracked

- **Total amount** of all completed donations
- **Number of donations** completed
- **Latest 10 donations** with details

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Dashboard
> **What to capture:** The Donations card with total, count, and recent donations table
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 4. How Donations Are Detected

A plan is treated as a donation plan when its Parameters contain:

```json
{
    "artist_donation": "1",
    "artist_user_id": "ARTIST_USER_ID"
}
```

See [Membership Plans](membership-plans.docs) for how to set this up.

---

## 5. Donation Recording Flow

When a user subscribes to a donation plan:

1. User purchases via Stripe Checkout
2. Payment succeeds
3. Membership Pro triggers `onAfterStoreSubscription`
4. DonationAdmin plugin:
   - Checks if the plan is a donation plan (`artist_donation=1`)
   - Records the donation in `#__artist_donations` table
   - Stores Stripe session ID and payment intent ID
   - Prevents duplicate recordings (checks last 60 seconds)

---

## 6. Database Table

Donations are stored in `#__artist_donations`:

| Column | Type | Description |
|--------|------|-------------|
| `id` | INT | Primary key |
| `artist_user_id` | INT | Artist receiving donation |
| `donor_user_id` | INT | Donor (may be NULL for guests) |
| `donor_name` | VARCHAR | Donor display name |
| `donor_email` | VARCHAR | Donor email address |
| `amount` | DECIMAL | Donation amount |
| `currency` | VARCHAR | Currency code (default: USD) |
| `stripe_session_id` | VARCHAR | Stripe Checkout Session ID |
| `stripe_payment_intent_id` | VARCHAR | Stripe Payment Intent ID |
| `status` | ENUM | pending / completed / failed |
| `artist_notified` | TINYINT | Whether artist was emailed |
| `created_at` | DATETIME | When donation was made |
| `updated_at` | DATETIME | Last update |

---

## 7. Frontend Text Overrides

The plugin automatically changes Membership Pro renewal language to donation-specific text:

| Original Text | Donation Text |
|--------------|--------------|
| Subscription Renewal | Make a Donation |
| Renew subscription for [PLAN_TITLE] | Donation for [PLAN_TITLE] |
| Process Renew | Complete Donation |
| Renew Membership | Make a Donation |

These can be customized — see [Language Overrides](language-overrides.docs).

---

## 8. Plugin Files

| File | Purpose |
|------|---------|
| `plugins/system/donationadmin/donationadmin.php` | Main plugin code |
| `plugins/system/donationadmin/donationadmin.xml` | Plugin manifest |
| `plugins/system/donationadmin/script.php` | Installer script |
| `plugins/system/donationadmin/override/helper.php` | OSMembership fee calculation override |
| `plugins/system/donationadmin/language/en-GB/*.ini` | Language files |

---

*For support, contact the development team.*
