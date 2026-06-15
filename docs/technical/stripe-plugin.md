# Technical Reference — os_stripe Payment Plugin

> **For:** Developers  
> **Covers:** Stripe integration, payment flow, configuration

---

## 1. File Location

```
components/com_osmembership/plugins/os_stripe.php
```

## 2. Class Overview

```php
class os_stripe extends os_payment
```

Extends `os_payment` (MPF Framework) with a `MPFPayment` base parent.

---

## 3. Configuration

Settings are stored via OSMembership admin:
**Components → Membership Pro → Configuration → Payment Methods → Stripe**

| Setting | Key | Description |
|---------|-----|-------------|
| Stripe Secret Key | `stripe_secret_key` | Live/Test mode secret (`sk_live_...` / `sk_test_...`) |
| Stripe Publishable Key | `stripe_publishable_key` | Live/Test mode public key (`pk_live_...` / `pk_test_...`) |
| Platform Fee % | `payment_fee_percent` | Platform commission (e.g., 20) |
| Donation Category ID | `donation_category_id` | Plan category for donation plans |

---

## 4. Key Methods

### `getTaskProcessor($task, $data)`
Routes payment tasks to handler methods.

### `processPayment($row, $data)`
Main entry point for all payments. Creates a Stripe Checkout Session.

```php
function processPayment($row, $data) {
    // 1. Determine plan type (recurring vs one-time)
    // 2. Resolve artist Stripe account
    // 3. Create Checkout Session with application fee
    // 4. Redirect user to Stripe Checkout
}
```

### `verifyPayment($sessionId)`
Verifies completed payment against Stripe API.

```php
function verifyPayment($sessionId) {
    // 1. GET /v1/checkout/sessions/{sessionId}
    // 2. Check payment_status === 'paid'
    // 3. Return true/false
}
```

### `getArtistUserIdForCheckout($row, $data)`
Resolves which artist user ID is associated with a checkout.

Logic:
1. Check `$data['artist_user_id']` (set in process payment)
2. Fallback to `$row->user_id`
3. Check `$row->plan_id` → category → find artist

### `getArtistStripeAccount($userId)`
Fetches artist's Stripe Connect account from `#__artist_stripe_accounts`.

### `createRecurringCheckoutSession($row, $data)`
Creates Stripe Checkout Session with `mode: subscription`.

### `createOneTimeCheckoutSession($row, $data)`
Creates Stripe Checkout Session with `mode: payment`.

---

## 5. Stripe API Calls

All API calls use direct cURL:

```php
$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.stripe.com/v1/checkout/sessions",
    CURLOPT_POST => true,
    CURLOPT_HTTPHEADER => [
        'Authorization: Bearer ' . $secretKey,
        'Content-Type: application/x-www-form-urlencoded',
    ],
    CURLOPT_POSTFIELDS => http_build_query($params),
    CURLOPT_RETURNTRANSFER => true,
]);
$response = curl_exec($ch);
```

### Endpoints Used

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/v1/checkout/sessions` | POST | Create checkout session |
| `/v1/checkout/sessions/{id}` | GET | Retrieve session status |
| `/v1/accounts` | POST | Create Connect account |
| `/v1/accounts/{id}` | GET | Retrieve account status |
| `/v1/account_links` | POST | Create onboarding link |

---

## 6. Platform Fee Logic

### One-Time Payments (Donations/Events)

```php
$applicationFee = intval($amountInCents * $feePercent / 100);
```

The fee is calculated as a percentage of the **total amount** (not net of Stripe fees).

### Recurring Payments (Fan Clubs)

```php
$applicationFeePercent = $feePercent; // e.g., 20
```

Stripe applies this percentage to each future recurring payment automatically.

---

## 7. Error Handling

Errors are logged via `$this->logError($message)` which writes to OSMembership's payment log.

Common errors:
- Missing Stripe API keys
- Artist hasn't completed Stripe onboarding
- Stripe account not in `charges_enabled` state
- Invalid plan/category configuration

---

## 8. Testing

Use Stripe test keys:

```
Secret Key:  sk_test_XXXXXXXXXXXXXXXXXXXXXXXX
Public Key:  pk_test_XXXXXXXXXXXXXXXXXXXXXXXX
```

Test card: `4242 4242 4242 4242` (any future date, any CVC)

Set `$testMode = 1` in the config to use test keys.

---

*For developer support, contact the development team.*
