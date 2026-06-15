# Technical Reference — ArtistDashboard Plugin

> **For:** Developers  
> **Covers:** Plugin architecture, dashboard features

---

## 1. File Location

```
plugins/community/artistdashboard/
├── artistdashboard.php        # Main plugin file
├── artistdashboard.xml        # Joomla manifest
├── artistdashboard.php        # JomSocial plugin class
└── language/
    └── en-GB/
        └── en-GB.plg_community_artistdashboard.ini
```

## 2. Class Overview

```php
class plgCommunityArtistDashboard extends CApplications
```

Extends JomSocial's `CApplications` base class.

---

## 3. Manifest

| Element | Value |
|---------|-------|
| Name | `plg_community_artistdashboard` |
| Type | `plugin` |
| Group | `community` |
| Dependencies | JomSocial, Membership Pro, Stripe |

---

## 4. Dashboard Tabs

The plugin adds tabs to the artist's profile/dashboard:

| Tab | View | Description |
|-----|------|-------------|
| **Payout Settings** | `payouts` | Stripe Connect onboarding, donations toggle |
| **Fan Clubs** | `fanclubs` | Fan club subscriber stats |
| **Paid Events** | `events` | Event ticket sales stats |

---

## 5. Payout Settings Tab

### Stripe Connect Onboarding

1. Artist clicks **Connect with Stripe**
2. Plugin creates a Stripe `account_link` (via os_stripe helper)
3. Artist completes Stripe Express onboarding
5. Artist is redirected back to dashboard
6. Plugin checks `charges_enabled` status

### Donations Toggle

Simple yes/no toggle stored in `#__artist_stripe_accounts.donations_enabled`.

When enabled, a Donate section appears on the artist's profile.

### Dashboard Display

```
┌────────────────────────────────────────────────┐
│  💰 Payout Settings                           │
│                                                │
│  Stripe Account:  ● Active                     │
│  Total Received:  $150.00                     │
│  Pending:         $25.00                      │
│                                                │
│  Donations:       [ON / OFF]                  │
│                                                │
│  [Disconnect]  [Refresh Status]               │
└────────────────────────────────────────────────┘
```

---

## 6. Fan Clubs Tab

Shows:

- Active subscribers count
- Monthly recurring revenue
- Link to manage plans (if applicable)

Data sourced from `#__osmembership_subscribers` joined with `#__osmembership_plans`.

---

## 7. Paid Events Tab

Shows:

- Upcoming events
- Past events
- Tickets sold
- Revenue

---

## 8. Integration Points

### Stripe Connect Status Sync

```php
function syncStripeAccount($userId) {
    $stripeAccountId = $this->getArtistAccountId($userId);
    $account = $this->stripeGetAccount($stripeAccountId);
    // Update charges_enabled, details_submitted, payouts_enabled
    $this->updateArtistAccount($userId, $account);
}
```

### Donate Button on Profile

When `donations_enabled = 1` for an artist, their profile displays a Donate form. The form calls `createDonateStripeSession()` which is part of the os_stripe plugin.

---

## 9. Database Tables

### `#__artist_stripe_accounts`

See [Database Tables → #__artist_stripe_accounts](database-tables.docs#1-__artist_stripe_accounts)

---

*For developer support, contact the development team.*
