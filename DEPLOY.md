# ProductionGuard.app — Deploy Instructions

## Step 1 — Host the APK

Upload the APK to a file host so it has a public download URL.

### Option A: GitHub Releases (Free, recommended)
1. Create a new GitHub repo (e.g. `production-guard-releases`)
2. Go to **Releases → Draft a new release**
3. Tag: `v1.0.0-beta`, upload `app-release.apk`
4. Publish release → copy the direct `.apk` asset URL
   Format: `https://github.com/YOUR_USER/production-guard-releases/releases/download/v1.0.0-beta/app-release.apk`

### Option B: Supabase Storage (You already have this)
1. Go to supabase.com → project `dkcecjxindnoxfflbsab` → Storage
2. Create a bucket `releases` (set to **Public**)
3. Upload `app-release.apk`
4. Click the file → **Get public URL**

---

## Step 2 — Update the APK link in index.html

In `index.html`, replace BOTH occurrences of:
```
YOUR_APK_DIRECT_LINK_HERE
```
with your actual APK URL from Step 1.

Search for: `YOUR_APK_DIRECT_LINK_HERE`
There are 2 places (nav button + download section button).

---

## Step 3 — Deploy the website

### Option A: Netlify (Recommended — Free + auto HTTPS)

1. Go to [netlify.com](https://netlify.com) → Sign up free
2. Click **Add new site → Deploy manually**
3. Drag & drop the entire `ProductionGuard/` folder onto the deploy window
4. Site deploys instantly to a random `.netlify.app` URL
5. In Netlify → **Site settings → Domain management → Add custom domain**
6. Enter: `productionguard.app`
7. Netlify gives you these DNS values to set in GoDaddy:

```
Type: A     Name: @      Value: 75.2.60.5
Type: CNAME Name: www    Value: YOUR-SITE.netlify.app
```

### Option B: GoDaddy Hosting (If you have a hosting plan)

1. Log into GoDaddy → **My Products → Hosting → Manage**
2. Open **cPanel → File Manager → public_html**
3. Upload `index.html` (and any other files) to `public_html/`
4. Your site is live at `productionguard.app` automatically (domain already on GoDaddy)

---

## Step 4 — Point GoDaddy DNS to Netlify

1. Log into GoDaddy → **My Products → Domains → productionguard.app → DNS**
2. Edit/Add these records:

| Type  | Name | Value              | TTL      |
|-------|------|--------------------|----------|
| A     | @    | 75.2.60.5          | 600 sec  |
| CNAME | www  | YOUR-SITE.netlify.app | 1 hour |

3. **Delete** any existing A records pointing elsewhere first
4. SSL cert auto-provisions on Netlify within ~5 minutes
5. DNS propagation: 10 min – 2 hours

---

## Step 5 — iOS (When Ready)

When iOS is ready (TestFlight or App Store):

1. Replace the iOS card's `href` in `index.html`:
   ```html
   <!-- TestFlight -->
   href="https://testflight.apple.com/join/YOUR_CODE"

   <!-- App Store -->
   href="https://apps.apple.com/app/idXXXXXXXXXX"
   ```
2. Remove the `coming-soon` class from the iOS button
3. Remove the `inactive` class from the iOS download card
4. Re-upload `index.html`

---

## Build a Release APK (Production-signed, smaller)

```bash
cd C:/Users/PTMaj/Desktop/production_guard
flutter build apk --release
```

APK will be at:
`build/app/outputs/flutter-apk/app-release.apk`

> Note: For a proper signed release build you'll need to set up a keystore.
> For beta distribution the debug APK works fine.

---

## File checklist
```
ProductionGuard/
├── index.html       ← main site (self-contained, no build needed)
└── DEPLOY.md        ← this file
```
