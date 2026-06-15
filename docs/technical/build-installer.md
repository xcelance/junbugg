# Technical Reference — Building an Installer Package

> **For:** Developers  
> **Covers:** Creating the installable zip package

---

## 1. Package Structure

```
pkg_juunbug_v1.0.0/
├── pkg_juunbug.xml                  # Package manifest
├── plg_system_donationadmin.zip     # System plugin
├── plg_community_artistdashboard.zip # Community plugin
└── os_stripe_payment.zip            # Payment plugin (os_stripe)
```

---

## 2. Package Manifest

Create `pkg_juunbug.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<extension type="package" version="4.0" method="upgrade">
    <name>JunBugg Package</name>
    <packagename>juunbug</packagename>
    <version>1.0.0</version>
    <description>JunBugg Artist Platform Package</description>

    <files>
        <file type="plugin" group="system" id="donationadmin">
            plg_system_donationadmin.zip
        </file>
        <file type="plugin" group="community" id="artistdashboard">
            plg_community_artistdashboard.zip
        </file>
        <file type="plugin" group="osmembership" id="os_stripe">
            os_stripe_payment.zip
        </file>
    </files>
</extension>
```

---

## 3. Plugin Zip Files

### DonationAdmin (`plg_system_donationadmin.zip`)

```
donationadmin/
├── donationadmin.php
├── donationadmin.xml
├── script.php
├── override/
│   └── helper.php
└── language/
    └── en-GB/
        ├── en-GB.plg_system_donationadmin.ini
        └── en-GB.plg_system_donationadmin.sys.ini
```

### ArtistDashboard (`plg_community_artistdashboard.zip`)

```
artistdashboard/
├── artistdashboard.php
├── artistdashboard.xml
└── language/
    └── en-GB/
        └── en-GB.plg_community_artistdashboard.ini
```

### os_stripe (`os_stripe_payment.zip`)

```
os_stripe/
└── os_stripe.php
```

Note: OSMembership payment plugins are a single file placed in `components/com_osmembership/plugins/`.

---

## 4. Build Script

```bash
#!/bin/bash
VERSION="1.0.0"
PACKAGE_DIR="pkg_juunbug_v${VERSION}"

mkdir -p "${PACKAGE_DIR}"

# Create DonationAdmin zip
cd plugins/system/donationadmin
zip -r "../../../${PACKAGE_DIR}/plg_system_donationadmin.zip" \
    donationadmin.php donationadmin.xml script.php override/ language/
cd ../../..

# Create ArtistDashboard zip
cd plugins/community/artistdashboard
zip -r "../../../${PACKAGE_DIR}/plg_community_artistdashboard.zip" \
    artistdashboard.php artistdashboard.xml language/
cd ../../..

# Create os_stripe zip
mkdir -p "${PACKAGE_DIR}/os_stripe"
cp components/com_osmembership/plugins/os_stripe.php \
   "${PACKAGE_DIR}/os_stripe/"
cd "${PACKAGE_DIR}/os_stripe"
zip -r "../os_stripe_payment.zip" os_stripe.php
cd ..
rm -rf os_stripe/

# Copy package manifest
cp pkg_juunbug.xml "${PACKAGE_DIR}/"

# Create final zip
zip -r "pkg_juunbug_v${VERSION}.zip" "${PACKAGE_DIR}/"
```

---

## 5. Installation

1. In Joomla admin: **System → Install → Extensions**
2. Upload `pkg_juunbug_v1.0.0.zip`
3. Joomla installs all 3 plugins
4. Enable plugins:
   - **System — DonationAdmin** (`plg_system_donationadmin`)
   - **Community — ArtistDashboard** (`plg_community_artistdashboard`)
   - **OSMembership — Stripe** (`os_stripe`)

5. Configure Stripe API keys: **Components → Membership Pro → Configuration → Payment Methods → Stripe**
6. Set **Donation Category ID** in DonationAdmin plugin settings

---

## 6. Post-Install Checklist

| Task | Where |
|------|-------|
| Enable DonationAdmin plugin | Extensions → Plugins |
| Enable ArtistDashboard plugin | Extensions → Plugins |
| Enable os_stripe plugin | Extensions → Plugins |
| Set Stripe API keys | Membership Pro → Configuration → Stripe |
| Set platform fee % | Membership Pro → Configuration → Stripe |
| Set Donation Category ID | DonationAdmin plugin settings |
| Create fan club plan(s) | Membership Pro → Plans |
| Create event plan(s) | Membership Pro → Plans |
| Create donation plan | Membership Pro → Plans |
| Create donation menu item | Menus → Main Menu (auto-created by installer) |

---

*For developer support, contact the development team.*
