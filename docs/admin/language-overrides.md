# Language Overrides

> **For:** Site administrators  
> **Covers:** Editing all text on donation pages

---

## 1. What Are Language Overrides?

Language overrides let you change any text on the site without editing PHP code. Overrides are stored in the database and take priority over the original language files.

---

## 2. Editable Strings

These are the donation-specific strings you can change:

| Constant | Default | Changes |
|----------|---------|---------|
| `PLG_DONATIONADMIN_RENEW_SUBSCRIPTION_PAGE_TITLE` | Donation for [PLAN_TITLE] | Browser tab/window title |
| `PLG_DONATIONADMIN_SUBSCRIION_RENEW_FORM_HEADING` | Make a Donation | Main heading on the donation form |
| `PLG_DONATIONADMIN_PROCESS_RENEW` | Complete Donation | Text on the submit button |
| `PLG_DONATIONADMIN_RENREW_MEMBERSHIP` | Make a Donation | Link/button text elsewhere |
| `PLG_DONATIONADMIN_RENREW_MEMBERSHIP_DESCRIPTION` | Please choose the donation option... | Description text |
| `PLG_DONATIONADMIN_FORM_MESSAGE` | Please choose the donation option... | Message box on the donation form |

---

## 3. How to Add an Override

1. Go to **Extensions → Language Overrides → New**
2. Set the following:

| Field | Value |
|-------|-------|
| Language | `English (en-GB)` |
| Client | `Administrator` |

3. Click **Select** next to **Language Constant**
4. In the search box, type `PLG_DONATIONADMIN_`
5. Click on the constant you want to override
6. In the **Text** field, enter your custom text
7. **Save & Close**

---

> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
> **📷 SCREENSHOT PLACEHOLDER**
>
> **Where:** Extensions → Language Overrides → New
> **What to capture:** Form with Language set to English (en-GB), Client set to Administrator, Language Constant search showing PLG_DONATIONADMIN_ results, and Text field
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
>
> ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## 4. Example: Changing the Button Text

Say you want the donate button to say **"Send Donation"** instead of **"Complete Donation"**:

1. Go to **Extensions → Language Overrides → New**
2. Language: `English (en-GB)`, Client: `Administrator`
3. Select constant: `PLG_DONATIONADMIN_PROCESS_RENEW`
4. Text: `Send Donation`
5. **Save & Close**
6. Clear cache

---

## 5. Using Placeholders

Some strings support placeholders that get replaced with real values:

| Placeholder | Replaced With | Example |
|-------------|---------------|---------|
| `[PLAN_TITLE]` | The plan name | "Donation for **Fan Club Monthly**" |

Use them like: `Support [PLAN_TITLE] — Make a Donation`

---

## 6. Clearing Cache

After adding or editing overrides, clear Joomla cache:

1. Go to **System → Clear Cache**
2. Select **Administrator** cache group
3. Click **Purge Expired**
4. Also go to **System → Clear Joomla Cache**
5. Click **Purge Expired**
6. Refresh the frontend page to see changes

If the old text still shows, try:

1. **Extensions → Plugins → Search "System - Page Cache"** (if enabled)
2. Disable it temporarily
3. Or clear your browser cache

---

## 7. Managing Existing Overrides

1. Go to **Extensions → Language Overrides**
2. Filter by **Client:** Administrator and **Language:** English (en-GB)
3. You'll see all your custom overrides
4. Click to edit or delete any override

---

## 8. Advanced: Adding New Constants

If you need a custom string that isn't listed above, it needs to be added to the plugin code first. Contact the development team.

---

*For support, contact the development team.*
