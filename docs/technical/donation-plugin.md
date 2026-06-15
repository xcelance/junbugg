# Technical Reference — DonationAdmin System Plugin

> **For:** Developers  
> **Covers:** Plugin architecture, events, overrides

---

## 1. File Location

```
plugins/system/donationadmin/
├── donationadmin.php          # Main plugin file
├── donationadmin.xml          # Joomla manifest
├── override/
│   └── helper.php            # OSMembership override helper
├── script.php                # Installer script
└── language/
    └── en-GB/
        ├── en-GB.plg_system_donationadmin.ini
        └── en-GB.plg_system_donationadmin.sys.ini
```

---

## 2. Manifest

| Element | Value |
|---------|-------|
| Name | `plg_system_donationadmin` |
| Type | `plugin` |
| Group | `system` |
| Joomla | 4.x / 5.x |
| PHP | 8.0+ |

XML: `donationadmin.xml`

---

## 3. Event Handlers

### `onAfterRoute()`

Sets a session flag when visiting the OSMembership registration page:

```php
function onAfterRoute() {
    $option = $app->getInput()->get('option');
    $view   = $app->getInput()->get('view');
    $planId = $app->getInput()->getInt('plan_id', 0);

    if ($option === 'com_osmembership' && $view === 'register' && $planId > 0) {
        $plan = OSMembershipHelper::getPlanDetail($planId);
        $categoryId = $plan->category_id ?? 0;
        $donationCategoryId = $this->params->get('donation_category_id', 0);

        if ($categoryId == $donationCategoryId) {
            $app->getSession()->set('donation_is_donation', 1);
        }
    }
}
```

### `onAfterRender()`

Modifies rendered HTML output:

```php
function onAfterRender() {
    if ($app->getSession()->get('donation_is_donation')) {
        // 1. Replace page title with donation-friendly title
        // 2. Replace donation form message text
        //    (text stored in DB, not language files — use regex)
        // 3. Clear session flag
    }
}
```

### `onAfterStoreSubscription($row, $data)`

Handles post-subscription actions:

```php
function onAfterStoreSubscription($row, $data) {
    if ($data->plan_category == $donationCategoryId) {
        // 1. Record donation in #__artist_donations
        // 2. Send notification to artist
    }
}
```

---

## 4. Language Override System

Rather than translating text at the PHP level, the plugin uses **Joomla Language Overrides** so all text is editable via **Extensions → Language(s) → Overrides**.

### Constants Defined

| Constant | Default Value | Context |
|----------|---------------|---------|
| `PLG_DONATIONADMIN_PAGE_TITLE` | Donation | Page title |
| `PLG_DONATIONADMIN_FORM_MESSAGE` | Choose your donation amount below. | Form description |
| `PLG_DONATIONADMIN_THANK_YOU` | Thank you for your donation! | Success message |

### How It Works

```php
use Joomla\CMS\Language\Text;

Text::_('PLG_DONATIONADMIN_PAGE_TITLE');
```

Users edit these strings via:
```
Extensions → Language(s) → Overrides → New
  Language Constant: PLG_DONATIONADMIN_PAGE_TITLE
  Text: [user's custom value]
```

---

## 5. OSMembership Helper Override

Deployed by `script.php` during installation:

```
components/com_osmembership/helper/override/helper.php
```

### `calculateDonationFee($amount, $feePercentage)`

```php
function calculateDonationFee($amount, $feePercentage) {
    $stripeFee = round($amount * 0.029 + 0.30, 2);
    $subtotal  = round($amount - $stripeFee, 2);
    $platform  = round($subtotal * $feePercentage / 100, 2);
    $artistPayout = round($subtotal - $platform, 2);

    return (object) [
        'stripe_fee'   => $stripeFee,
        'subtotal'     => $subtotal,
        'platform_fee' => $platform,
        'artist_payout'=> $artistPayout,
    ];
}
```

---

## 6. Installer Script (`script.php`)

| Method | Action |
|--------|--------|
| `install()` | Creates `#__artist_donations` table |
| | Deploys override helper file |
| | Creates donation menu item |
| | Sets default params |
| `uninstall()` | Drops `#__artist_donations` table |
| | Removes override helper file |
| | Removes menu item |
| `update()` | Same as install (add/update tables) |

---

## 7. Database

### `#__artist_donations`

| Column | Type | Description |
|--------|------|-------------|
| `id` | INT(11) PK | Auto-increment |
| `artist_user_id` | INT(11) | Artist user ID |
| `donor_email` | VARCHAR(255) | Donor email |
| `amount` | DECIMAL(10,2) | Donation amount |
| `fee` | DECIMAL(10,2) | Platform fee |
| `net_amount` | DECIMAL(10,2) | Artist payout |
| `message` | TEXT | Donor message |
| `session_id` | VARCHAR(255) | Stripe Session ID |
| `payment_intent` | VARCHAR(255) | Stripe PaymentIntent |
| `status` | VARCHAR(50) | pending / completed / failed |
| `created_at` | DATETIME | Timestamp |
| `updated_at` | DATETIME | Timestamp |

---

*For developer support, contact the development team.*
