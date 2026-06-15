# Membership Plans

> **For:** Site administrators  
> **Covers:** Creating & managing all plan types

---

## 1. Plan Types Overview

Membership Pro supports three plan types on JunBugg:

| Plan Type | Purpose | Recurring | Example Price |
|-----------|---------|-----------|--------------|
| **Fan Club** | Monthly subscription to support an artist | Yes | $5/month |
| **Paid Event** | One-time payment for event access | No | $2 |
| **Donation** | One-time tip to an artist | No | Any amount |

---

## 2. Create a Basic Plan

1. Go to **Membership Pro → Plans → New**
2. Fill in the form:

### General Tab

| Field | Fan Club Example | Event Example | Donation Example |
|-------|-----------------|---------------|-----------------|
| Title | `Fan Club — [Artist Name]` | `Event: [Event Name]` | `Donation to [Artist Name]` |
| Alias | *(auto-fills)* | *(auto-fills)* | *(auto-fills)* |
| Price | `5.00` | `2.00` | `0.00` |
| Duration | `1` | `1` | `1` |
| Duration Unit | `Month` | `Day` | `Day` |
| Recurring | `Yes` | `No` | `No` |

### Subscription Settings Tab

| Field | Fan Club | Event | Donation |
|-------|----------|-------|----------|
| Enable Auto Subscribe | No | No | No |
| Require Login | Yes | Yes | No (optional) |
| Trial Period | Empty | Empty | Empty |

### Parameters Tab (Donation-specific)

For **donation plans only**, add this JSON in the Parameters field:

```json
{
    "artist_donation": "1",
    "artist_user_id": "ARTIST_USER_ID"
}
```

Replace `ARTIST_USER_ID` with the actual Joomla user ID of the artist.

3. **Save & Close**

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Plans → New
> **What to capture:** Plan creation form with all tabs visible
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 3. Plan Categories

Group plans into categories for easier management.

1. Go to **Membership Pro → Categories → New**
2. Create categories like:
   - `Fan Clubs`
   - `Events`
   - `Donations`
3. Assign plans to categories when creating/editing plans

---

## 4. Renew Rates (For Recurring Plans)

For fan club plans, you can offer multiple renewal options:

1. Open a recurring plan
2. Go to **Renew Rates** tab
3. Add options:

| Option | Duration | Price |
|--------|----------|-------|
| Monthly | 1 Month | $5.00 |
| Quarterly | 3 Months | $13.50 |
| Yearly | 12 Months | $48.00 |

---

## 5. Upgrading/Downgrading Plans

Members can move between plans:

1. Open a plan
2. Go to **Upgrade Rules** tab
3. Add upgrade/downgrade paths to other plans
4. Set prorated pricing if needed

---

## 6. Plan Visibility

| Setting | Effect |
|---------|--------|
| Published = Yes | Plan is visible and purchasable |
| Published = No | Plan is hidden but existing subscriptions continue |
| Access = Public | Anyone can purchase |
| Access = Registered | Only logged-in users can purchase |
| Access = Special | Only specific user groups can purchase |

---

## 7. Testing a Plan

1. Set Stripe plugin to **Test** mode (see [Stripe Config](stripe-config.docs))
2. As a test user, go to the frontend and attempt to purchase the plan
3. Use test card: `4242 4242 4242 4242`
4. Verify:
   - Stripe Checkout opens correctly
   - Payment succeeds
   - User returns to success page
   - Subscription record is created in Membership Pro

---

## 8. Managing Existing Subscriptions

Go to **Membership Pro → Subscribers** to:

- View all active subscriptions
- Manually create or cancel subscriptions
- View payment history
- Send subscription emails

---

*For support, contact the development team.*
