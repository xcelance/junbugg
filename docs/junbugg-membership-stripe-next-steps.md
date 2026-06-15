# JunBugg Membership Pro + Stripe Notes

## Current status

- Membership Pro is installed on the Joomla/JomSocial dev site.
- A custom Membership Pro payment plugin named `os_stripe` was created.
- Joomla user group `Artists` was created under `Registered`.
- JomSocial multi-profile type `Artist Profile` was created.
- A user can choose/switch their profile type to `Artist Profile`.
- Correct Joomla installer package:
  `/var/www/html/juunbug/build/os_stripe_membership_pro_file_installer.zip`
- This plugin installs/registers `os_stripe` in Membership Pro and adds Stripe Checkout support for:
  - one-time Membership Pro plans
  - recurring Membership Pro plans
  - subscription activation after successful Stripe checkout
  - cancel-at-period-end for recurring subscriptions

## Immediate next steps

1. Go to Joomla Admin -> System -> Install Extensions.
2. Upload:
   `/var/www/html/juunbug/build/os_stripe_membership_pro_file_installer.zip`
3. Go to Membership Pro -> Plugins.
4. Open `os_stripe` / `Stripe Checkout`.
5. Publish/enable it.
6. Set Mode to `Test`.
7. Add Stripe test keys:
   - Publishable key: `pk_test_...`
   - Secret key: `sk_test_...`
8. Set currency to `usd` first for testing.

## Artist profile setup

### Dashboard setup completed

- JomSocial Multiple Profiles is enabled.
- `Artist Profile` was added in JomSocial Multiple Profiles.
- `Artist Profile` is assigned to the Joomla `Artists` user group.
- Artist-specific fields can be included in `Artist Profile`, including:
  - Creator Name
  - Bio / About
  - Services Offered
  - Business Address
  - Website
  - Email
  - Business Phone
  - Cell Phone
  - Operating Hours
  - Social links

### How a user becomes an artist

1. User logs in on the frontend.
2. User goes to their profile/account edit area.
3. User changes profile type from `Personal Profile` to `Artist Profile`.
4. Save the profile.
5. The user now has artist profile fields and can be treated as an artist.

### Artist Dashboard plugin

- We created a new JomSocial community plugin package named `artistdashboard`.
- It renders an artist-only dashboard area inside the JomSocial profile page.
- All visible labels use Joomla language keys so translation overrides can change them.
- The plugin package includes an installer script so it can auto-enable itself on install/update.
- Install package path: `build/artistdashboard.zip`
- The ZIP was rebuilt in the workspace because the live Joomla build folder was read-only from the current session.

### Important permission note

- `Artist Profile` controls the JomSocial profile type and visible profile fields.
- Joomla group `Artists` should be used as the secure permission check for monetization tools.
- If JomSocial does not automatically add the user to the `Artists` group after switching profile type, add/sync this in code:
  - `Artist Profile` -> add user to `Artists`
  - `Personal Profile` -> remove user from `Artists`

## Artist/fan role logic

- Fans/users:
  - normal `Registered` users
  - can donate, join fan clubs, and pay for events
- Artists:
  - `Artist Profile`
  - should also be in Joomla group `Artists`
  - can connect Stripe, receive donations, set fan club prices, and create paid events

## Test plans to create

- `Fan Club Monthly`
  - recurring monthly
  - test price: `$5`
- `Paid Event Access`
  - one-time payment
  - test price: `$2`

## Checkout test

Use Stripe test card:

```text
4242 4242 4242 4242
```

Use any future expiry date and any CVC.

Expected result:

- Stripe Checkout opens.
- Payment succeeds.
- User returns to Membership Pro complete page.
- Membership Pro subscriber record becomes active.

## Next custom work after payment test

### JomSocial Fan Club mapping

- Add mapping between Membership Pro plan and JomSocial group.
- Fan Club groups remain free by default.
- Artist can switch a group to paid and select/set the Membership Pro plan.
- Successful subscription grants group/fan club access.
- Canceled or expired subscription removes/locks paid access.

### JomSocial Paid Event mapping

- Add mapping between Membership Pro plan and JomSocial event.
- Events/listening parties remain free by default.
- Artist can switch an event to paid and set ticket price.
- Successful one-time payment approves/allows event access.

### Donation flow

- Build fast donation flow for artist profiles.
- Use Stripe Checkout one-time payment.
- Keep the donation path minimal so first-time users can complete quickly.
- Record donation and notify artist after successful payment.

### Stripe Connect split

- Add artist Stripe account connection/status.
- Store artist Stripe connected account ID.
- Extend Stripe Checkout creation to include Connect split:
  - 80% to artist
  - 20% to JunBugg platform
- This split requires custom code beyond normal Membership Pro payment behavior.

## Notes

- `pkg_payments_v3.1.8.zip` is a separate Stripe payments component, not a Membership Pro payment plugin.
- `os_eb_stripe` is for Events Booking, not Membership Pro.
- The custom `os_stripe` plugin is the current path for Membership Pro Stripe checkout.
