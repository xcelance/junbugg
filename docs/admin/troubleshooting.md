# Troubleshooting

> **For:** Site administrators  
> **Covers:** Common issues, errors, and solutions

---

## 1. Payment Issues

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| Stripe Checkout doesn't open when clicking pay | API keys missing or incorrect | Check **Membership Pro → Plugins → Stripe Checkout** — keys must be entered |
| Stripe Checkout opens but shows error | Mode mismatch (test vs live) | If using test keys, set Mode to `Test`. If live keys, set Mode to `Live` |
| "No such payment method" error | `os_stripe` not published | Go to **Membership Pro → Plugins** and publish **Stripe Checkout** |
| Payment fails at Stripe | Invalid test card or declined | Use `4242 4242 4242 4242` with any future expiry |
| "This transaction cannot be processed" | Cross-country restrictions | Artist's Stripe account country must match platform country (see Stripe Connect setup) |
| User not redirected back after payment | Success URL not set correctly | Check the Stripe plugin config for return URL settings |

### Debugging Payments

1. Enable **IPN Log** in Stripe plugin settings
2. Check logs at: **Membership Pro → Plugins → Stripe Checkout → IPN Log**
3. Or check PHP error logs

---

## 2. Donation Issues

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| Donate button not showing on artist profile | Artist hasn't enabled donations | Artist must go to their Dashboard → toggle **Donations** ON |
| "Donation plan not found" error | Plan missing `artist_donation` param | Edit the plan → add `"artist_donation": "1"` in Parameters JSON |
| Custom amount not working | Amount below minimum ($0.50) | Minimum custom donation is $0.50 |
| Donation card not showing in admin dashboard | DonationAdmin plugin disabled | Go to **Extensions → Plugins → System - Donation Admin** → Enable |
| Donation not recorded after successful payment | Plugin error or duplicate prevention | Check `#__artist_donations` table — may be duplicate protected (same amount within 60s) |

---

## 3. Stripe Connect / Artist Payout Issues

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| "Connect with Stripe" button not showing | ArtistDashboard plugin not enabled | Go to **Extensions → Plugins** and enable **Community - Artist Dashboard** |
| Stripe onboarding fails | Browser popup blocker | Allow popups for the site |
| "Country not supported" | Artist's country not configured | Contact dev team to add the country |
| Artist says "I can't receive payouts" | KYC not completed | Artist should log into Stripe Express and complete requirements |
| Payouts pending for days | New Stripe account | First payouts can take 7-14 days |
| Platform fee not being deducted | Fee percentage set to 0% | Check **Payment Processing Fee (%)** in Stripe plugin settings |

### Checking Account Status

Go to Stripe Dashboard → **Connect → Accounts** to see:

- Account status (active/restricted)
- Requirements due
- Payout schedule

---

## 4. Subscription Issues

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| Subscription not activating after payment | Payment pending | Check Stripe Dashboard → Payments for status |
| Recurring payment not charging | Stripe subscription incomplete | Check Stripe Dashboard → Subscriptions |
| Cancel subscription not working | Plugin not handling webhooks | Contact dev team |
| Member complaining about double charge | They may have two subscriptions | Check **Membership Pro → Subscribers** for duplicates |

---

## 5. Language Override Issues

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| Text not changing after adding override | Cache not cleared | Go to **System → Clear Cache** → Purge Expired |
| Text still shows old value | Wrong client selected | When creating override, set **Client** to `Administrator` |
| Override not found in search | Typo in constant name | Search for just `PLG_DONATIONADMIN` (partial match works) |
| HTML tags showing as raw text | Client set to Site instead of Administrator | Edit the override → change **Client** to `Administrator` |

---

## 6. Plugin Installation Issues

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| "Invalid extension type" | Wrong ZIP format | Make sure you're uploading the correct installer package |
| File not found after install | Wrong install method | Use **Extensions → Install Extensions**, not FTP |
| Database tables not created | Installer script failed | Contact dev team to create tables manually |
| Plugin installed but not working | Not enabled | Go to **Extensions → Plugins** and enable it |

---

## 7. PHP Errors

If you see error pages or blank screens:

### Check PHP Error Log

```bash
# Common log locations
/var/log/apache2/error.log
/var/log/php_errors.log
```

### Common PHP Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `getCatalogue(): Return value must be...` | Outdated donation plugin | Update to latest DonationAdmin plugin version |
| `Class "OSMembershipHelperOverride..." not found` | Override files not deployed | Reinstall ArtistDashboard plugin |
| `Call to undefined method...` | Plugin version mismatch | Make sure all plugin versions are compatible |

---

## 8. Getting Help

When contacting support, provide:

1. **Joomla version** (System → System Information)
2. **PHP version**
3. **Plugin versions** (Membership Pro, os_stripe, DonationAdmin, ArtistDashboard)
4. **Stripe mode** (Test or Live)
5. **Full error message** (screenshot or copy-paste)
6. **Steps to reproduce** the issue

---

*For support, contact the development team.*
