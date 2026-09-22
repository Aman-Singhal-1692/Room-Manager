# Room Manager — Google Drive Sync Setup

This app stores its data as a JSON file called `room-manager-data.json` in your own
Google Drive (in a folder visible to you), and syncs it every time you open the app
on any device. You need to do two one-time things: create a free Google OAuth app,
and put the app online somewhere.

Total time: about 15–20 minutes, once.

---

## Part 1 — Create your Google OAuth Client ID

1. Go to **https://console.cloud.google.com/** and sign in with the Google account
   whose Drive you want to sync to.
2. Click the project dropdown (top left) → **New Project**. Name it something like
   `Room Manager` → **Create**. Wait a few seconds, then make sure it's selected.
3. In the left sidebar (or search bar), go to **APIs & Services → Library**.
   Search for **Google Drive API** → click it → **Enable**.
4. Go to **APIs & Services → OAuth consent screen**.
   - User Type: **External** → Create.
   - App name: `Room Manager` (or anything). User support email: your email.
     Developer contact: your email. Save and continue through the remaining
     screens (Scopes, Test users) — you don't need to add anything there yet.
   - On the **Test users** step, click **Add users** and add your own Google
     email address. This lets you sign in without Google requiring a full
     public app review (fine for personal/business use with a handful of
     staff accounts).
   - Save and finish.
5. Go to **APIs & Services → Credentials** → **Create Credentials** →
   **OAuth client ID**.
   - Application type: **Web application**.
   - Name: `Room Manager Web`.
   - Under **Authorized JavaScript origins**, click **Add URI** and enter the
     exact web address where you will host this app (see Part 2 first if you
     don't have it yet — you can come back and add it after deploying).
     Example: `https://room-manager-aman.netlify.app`
   - Click **Create**. Copy the **Client ID** shown (looks like
     `123456-abc.apps.googleusercontent.com`).
6. Open `config.js` in this folder and replace `YOUR_CLIENT_ID_HERE.apps.googleusercontent.com`
   with the Client ID you copied. Save the file.

**Important:** every time you deploy this app to a new URL, come back to
Credentials → your OAuth client → **Authorized JavaScript origins** and add
that new URL, or sign-in will fail with a "redirect_uri_mismatch" style error.

---

## Part 2 — Put the app online (free, no coding required)

You need a real `https://` address for Google sign-in to work — it will not
work by double-clicking the HTML file on your computer.

**Easiest option: Netlify Drop**

1. Go to **https://app.netlify.com/drop**
2. Sign up for a free account (email or Google).
3. Drag the whole `room-manager-drive` folder (the one with `index.html`,
   `config.js`, this README) onto the page.
4. Netlify gives you a live link like `https://random-name-123.netlify.app`.
   You can rename it to something memorable in **Site settings → Change site name**.
5. Copy that link, go back to Google Cloud Console → Credentials → your OAuth
   client → **Authorized JavaScript origins** → add it (just the
   `https://yourname.netlify.app` part, no trailing slash or path).
6. Open the link. Click **Sign in with Google**, approve access, and you're syncing.

**Alternative: GitHub Pages** works the same way if you already use GitHub —
push this folder to a repo, enable Pages in repo Settings, and use the Pages
URL as your authorized origin instead.

---

## How it works day to day

- Open the app link on your phone, laptop, or front-desk PC — sign in once
  per device, and every change (checking a guest in/out, adding a room) saves
  to `room-manager-data.json` in your Drive automatically, a second or two
  after you make it.
- Opening the app on another signed-in device pulls the latest data from
  Drive on load, so all devices stay in sync.
- You can find the backup file itself in your Google Drive by searching for
  `room-manager-data.json` — safe to leave alone, the app manages it.
- If you're offline, the app keeps working from the last-synced local copy
  and re-syncs once you're back online and reload.
- Click **Disconnect** in the sidebar to sign out of Drive on that device;
  the app then falls back to that device's local storage only.

## Troubleshooting

- **"Not configured" next to Google Drive**: you haven't put your Client ID
  into `config.js` yet.
- **Sign-in popup closes immediately / errors**: the site's URL isn't listed
  under Authorized JavaScript origins for your OAuth client — add it exactly
  (matching `https://`, domain, and no trailing slash).
- **"Access blocked: app not verified"**: make sure your Google account is
  added as a Test user on the OAuth consent screen (Part 1, step 4).
