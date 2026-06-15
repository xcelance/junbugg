# JunBugg — Setup Guide

> **For:** Site administrators  
> **Prerequisites:** Joomla 4/5 installed, admin access

---

## 1. Install Required Extensions

### 1.1 Install Membership Pro

If not already installed:

1. Go to **Extensions → Install Extensions**
2. Upload the Membership Pro package (provided separately)
3. Follow the installer prompts

### 1.2 Install os_stripe Payment Plugin

The `os_stripe` plugin adds Stripe Checkout as a payment method in Membership Pro.

1. Go to **Extensions → Install Extensions**
2. Upload `os_stripe_membership_pro_file_installer.zip`
3. After installation, the plugin file lives at:  
   `components/com_osmembership/plugins/os_stripe.php`

### 1.3 Publish & Configure os_stripe

1. Go to **Membership Pro → Plugins**
2. Find **Stripe Checkout** (`os_stripe`)
3. Click to open and configure:

| Setting | Test Value | Live Value |
|---------|-----------|------------|
| Mode | `Test` | `Live` |
| Test Publishable Key | `pk_test_...` | — |
| Test Secret Key | `sk_test_...` | — |
| Live Publishable Key | — | `pk_live_...` |
| Live Secret Key | — | `sk_live_...` |
| Currency | `USD` | `USD` |
| Payment Processing Fee (%) | `20` | `20` |

4. **Save & Close**

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Plugins → Stripe Checkout
> **What to capture:** Full config page with all fields
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### 1.4 Install DonationAdmin System Plugin

1. Go to **Extensions → Install Extensions**
2. Upload `donationadmin.zip`
3. Go to **Extensions → Plugins**
4. Search for **Donation Admin**
5. Open it and set **Status** to **Enabled**
6. **Save & Close**

### 1.5 Install ArtistDashboard Plugin

1. Go to **Extensions → Install Extensions**
2. Upload `artistdashboard.zip`
3. The installer automatically:
   - Copies override files to `components/com_osmembership/helper/override/`
   - Creates necessary database tables
   - Enables the plugin

---

## 2. Create Database Tables

The installer scripts create these tables automatically, but verify they exist:

| Table | Purpose | Created By |
|-------|---------|-----------|
| `#__artist_donations` | Tracks all donations with Stripe session IDs | DonationAdmin plugin |
| `#__artist_stripe_accounts` | Artist Stripe Connect accounts & status | ArtistDashboard plugin |
| `#__osmembership_plans` | Membership plans (existing) | Membership Pro |

To verify: Use **Extensions → Database → Fix** or check via phpMyAdmin.

---

## 3. Set Up Joomla User Groups

1. Go to **Users → Groups**
2. Confirm the **Artists** group exists under **Registered**
3. If not, create it:

   - **Parent:** Registered
   - **Title:** Artists

---

## 4. Configure Membership Pro

### 4.1 General Settings

1. Go to **Membership Pro → Configuration**
2. Set:
   - **Currency:** `USD`
   - **Payment Plugin:** Ensure `Stripe Checkout` is listed
3. **Save**

### 4.2 Create a Test Donation Plan

1. Go to **Membership Pro → Plans → New**
2. Fill in:

| Field | Test Value |
|-------|-----------|
| Title | `Test Donation` |
| Price | `5.00` |
| Duration | `1 Day` |
| Recurring | `No` |

3. In the **Parameters** field (JSON):  
   ```json
   {
       "artist_donation": "1",
       "artist_user_id": "USER_ID_HERE"
   }
   ```
4. **Save**

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Membership Pro → Plans → New (Parameters tab)
> **What to capture:** Plan form with title, price, duration, and Parameters JSON
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 5. Verify Installation

Run through this checklist:

- [ ] `os_stripe` plugin published in Membership Pro → Plugins
- [ ] Stripe API keys entered (test mode)
- [ ] DonationAdmin plugin enabled in Extensions → Plugins
- [ ] `#__artist_donations` table exists
- [ ] `#__artist_stripe_accounts` table exists
- [ ] At least one test donation plan created
- [ ] Artists user group exists
- [ ] ArtistDashboard plugin installed & enabled

---

## 6. Next Steps

- [Test a payment](TODO) with the 4242 test card
- [Configure Stripe Connect](admin/stripe-config.docs) for artist payouts
- [Set up language overrides](admin/language-overrides.docs) to customize text
- Read the [Client Guide](../README.docs) for a full feature overview

---

*For support, contact the development team.*
