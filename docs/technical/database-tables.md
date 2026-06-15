# Technical Reference — Database Tables

> **For:** Developers  
> **Covers:** Table schemas and relationships

---

## 1. `#__artist_stripe_accounts`

Artist Stripe Connect account storage.

| Column | Type | Description |
|--------|------|-------------|
| `user_id` | INT(11) PK | Joomla user ID |
| `stripe_account_id` | VARCHAR(255) | Stripe Connect account ID (`acct_...`) |
| `onboarding_completed` | TINYINT(1) | Whether onboarding is complete |
| `charges_enabled` | TINYINT(1) | Stripe charges enabled |
| `details_submitted` | TINYINT(1) | Stripe details submitted |
| `payouts_enabled` | TINYINT(1) | Stripe payouts enabled |
| `donations_enabled` | TINYINT(1) | Artist-opt in for donations |
| `account_balance` | DECIMAL(10,2) | Cached balance |
| `pending_balance` | DECIMAL(10,2) | Cached pending balance |
| `created_at` | DATETIME | Record creation time |
| `updated_at` | DATETIME | Last update timestamp |

**Relationships:**
- `user_id` → `#__users.id`

---

## 2. `#__artist_donations`

Individual donation records.

| Column | Type | Description |
|--------|------|-------------|
| `id` | INT(11) AUTO_INCREMENT PK | Record ID |
| `artist_user_id` | INT(11) | Artist user ID |
| `donor_email` | VARCHAR(255) | Donor email (nullable) |
| `amount` | DECIMAL(10,2) | Donation amount |
| `fee` | DECIMAL(10,2) | Platform fee |
| `net_amount` | DECIMAL(10,2) | Amount after fees |
| `message` | TEXT | Donor message (nullable) |
| `session_id` | VARCHAR(255) | Stripe Checkout session ID |
| `payment_intent` | VARCHAR(255) | Stripe PaymentIntent ID |
| `status` | VARCHAR(50) | completed / pending / failed |
| `created_at` | DATETIME | Donation timestamp |
| `updated_at` | DATETIME | Last update |

**Relationships:**
- `artist_user_id` → `#__users.id`

---

## 3. Core OSMembership Tables (relevant)

### `#__osmembership_plans`

| Column | Type | Description |
|--------|------|-------------|
| `id` | INT(11) PK | Plan ID |
| `title` | VARCHAR(255) | Plan name |
| `price` | DECIMAL(10,2) | Plan price |
| `category_id` | INT(11) | Category (fan club / event / donation) |
| `recurring` | TINYINT(1) | Is recurring subscription |
| `trial_duration` | INT(11) | Trial duration (days) |
| `trial_amount` | DECIMAL(10,2) | Trial price |

### `#__osmembership_subscribers`

| Column | Type | Description |
|--------|------|-------------|
| `id` | INT(11) PK | Subscription ID |
| `user_id` | INT(11) | Subscriber user ID |
| `plan_id` | INT(11) | Plan ID |
| `created_date` | DATETIME | Subscription start |
| `subscription_date_to` | DATETIME | Subscription end |
| `published` | TINYINT(1) | Active/inactive |
| `gross_amount` | DECIMAL(10,2) | Paid amount |
| `fee_amount` | DECIMAL(10,2) | OSMembership fee |

---

## 4. Table Relationships Diagram

```
#__users
  ├── #__artist_stripe_accounts (user_id)
  ├── #__artist_donations (artist_user_id)
  └── #__osmembership_subscribers (user_id)
        └── #__osmembership_plans (plan_id)
```

---

## 5. Useful Queries

### Total donations per artist
```sql
SELECT artist_user_id, SUM(amount) as total
FROM #__artist_donations
WHERE status = 'completed'
GROUP BY artist_user_id;
```

### Artists with completed onboarding
```sql
SELECT u.username, a.stripe_account_id
FROM #__artist_stripe_accounts a
JOIN #__users u ON u.id = a.user_id
WHERE a.onboarding_completed = 1 AND a.charges_enabled = 1;
```

### Active fan club subscriptions
```sql
SELECT p.title, COUNT(s.id) as subscribers
FROM #__osmembership_subscribers s
JOIN #__osmembership_plans p ON p.id = s.plan_id
WHERE s.published = 1 AND p.recurring = 1
GROUP BY p.title;
```

---

*For developer support, contact the development team.*
