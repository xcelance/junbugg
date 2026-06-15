# Stripe Configuration

> **For:** Site administrators  
> **Covers:** Stripe account setup, API keys, live mode, platform fee

---

## 1. Create a Stripe Account

If you don't have one already:

1. Go to [https://dashboard.stripe.com/register](https://dashboard.stripe.com/register)
2. Enter your email, name, and password
3. Complete the business details:
   - Country
   - Business type (Individual or Company)
   - Business details
4. Verify your email

---

## 2. Get API Keys

### Test Keys (for development)

1. Log in to [Stripe Dashboard](https://dashboard.stripe.com/)
2. Go to **Developers → API Keys**
3. Copy the **Publishable key** (starts with `pk_test_`)
4. Copy the **Secret key** (starts with `sk_test_`)

### Live Keys (for production)

1. In Stripe Dashboard, go to **Developers → API Keys**
2. Toggle to **Live mode** (top-right switch)
3. Copy the **Publishable key** (starts with `pk_live_`)
4. Copy the **Secret key** (starts with `sk_live_`)

> **⚠️ Security:** Never share your secret key. Never commit it to code repositories.

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Stripe Dashboard → Developers → API Keys
> **What to capture:** The API Keys page showing both Publishable and Secret keys (blur the actual key values)
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 3. Enter Keys in Joomla

1. Go to **Membership Pro → Plugins**
2. Click **Stripe Checkout** (`os_stripe`)
3. Fill in the fields:

### Test Mode

| Field | Value |
|-------|-------|
| Mode | `Test` |
| Test Publishable Key | `pk_test_51...` |
| Test Secret Key | `sk_test_51...` |

### Live Mode

| Field | Value |
|-------|-------|
| Mode | `Live` |
| Live Publishable Key | `pk_live_51...` |
| Live Secret Key | `sk_live_51...` |

4. **Save & Close**

---

## 4. Platform Fee (Commission)

The platform fee is the percentage JunBugg takes from each transaction.

### How It Works

```
Payment:          $10.00
Stripe Fee:      -$0.30 + 2.9%
Platform Fee:    -20% of subtotal
Artist Receives:  ~$7.76
```

### Change the Fee

1. Go to **Membership Pro → Plugins → Stripe Checkout**
2. Find **Payment Processing Fee (%)**
3. Default: `20` (20%)
4. Change to any value (e.g., `15` for 15%, `0` for no fee)
5. **Save**

---

## 5. Configure Stripe Express (for Artist Payouts)

Stripe Express is used for Artist onboarding so they can receive payouts.

### Set Your Platform Details

1. In Stripe Dashboard, go to **Settings → Connect → Express**
2. Configure:
   - **Business name:** JunBugg (or your site name)
   - **Icon:** Upload a small logo
   - **Brand color:** Your brand color
   - **Onboarding completion:** Redirect to your site
3. Save

### Supported Countries

Artists can connect from these countries (configurable):

| Country | Currency |
|---------|----------|
| Australia | AUD |
| United States | USD |
| United Kingdom | GBP |
| Canada | CAD |
| *More available on request* |

---

## 6. Switch to Live Mode

### Pre-Flight Checklist

- [ ] All Membership Pro plans are set up correctly
- [ ] Test payments succeed with the 4242 test card
- [ ] Artist Stripe Connect onboarding works
- [ ] Donation flow works end-to-end
- [ ] Language overrides are configured

### Steps

1. Go to **Membership Pro → Plugins → Stripe Checkout**
2. Change **Mode** to `Live`
3. Replace test keys with live keys
4. **Save**
5. Run a real transaction with a small amount ($1) to verify

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Plugins → Stripe Checkout (Live mode)
> **What to capture:** Plugin settings with Mode set to Live and live keys filled in
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 7. Stripe Dashboard Monitoring

After going live, monitor payments in Stripe Dashboard:

| Section | What To Check |
|---------|---------------|
| **Payments** | All completed, pending, failed payments |
| **Connect → Transfers** | Artist payouts |
| **Connect → Accounts** | Artist onboarding status |
| **Balance** | Available payout balance |
| **Reports** | Transaction reports & reconciliation |

---

*For support, contact the development team.*
