# André & Shérine — Wedding Invitation (Production Build)

A self-contained static website. **No backend, database, or server-side code is required** —
just upload these files to your web host.

## What's in this folder

```
production/
├── index.html          ← the invitation page (120 KB, loads instantly)
├── assets/
│   ├── img-01.png       ← monogram cutout (transparent)
│   ├── img-02.jpg       ← photo
│   ├── img-03.jpg       ← photo
│   ├── img-04.jpg       ← photo
│   ├── img-05.jpg       ← photo
│   └── music.mp3        ← background music
├── og-image.png         ← preview shown when the link is shared (WhatsApp/iMessage/etc.)
├── favicon.svg          ← browser-tab icon
├── site.webmanifest     ← PWA / "add to home screen" metadata
├── robots.txt           ← keeps the page out of search engines (privacy)
├── .htaccess            ← Apache / LiteSpeed / cPanel config (compression + caching)
└── web.config           ← IIS / Windows hosting config (use instead of .htaccess)
```

The original single-file invitation was **58 MB** (all media embedded as base64). This build
externalizes and optimizes the media, bringing it down to **~11.7 MB total** with an
instant-loading HTML page. Photos were re-encoded to high-quality JPEG (≈87% smaller, no
visible quality loss); the transparent monogram stays PNG.

## ⚠️ One thing to finish before publishing: set your domain

Open `index.html` and replace **`YOUR-DOMAIN`** (appears in 4 places, all in the `<head>`)
with your real domain, e.g. `invite.example.com`. This only affects the link-share preview
(Open Graph) and the canonical URL — the page itself works without it.

Quick find-and-replace (PowerShell, run inside this folder):

```powershell
(Get-Content index.html -Raw) -replace 'YOUR-DOMAIN','invite.example.com' |
  Set-Content index.html -Encoding utf8 -NoNewline
```

## ⚠️ Second thing to finish: connect the RSVP form (Google Sheet)

The "Will you be there?" section has a real RSVP form (name · attending/declining ·
number of guests). Every submission drops into a **Google Sheet** as a row — with a live
headcount at the top — and emails you a copy. The two phone numbers (bride & groom) remain
for calls/messages.

Full step-by-step is in **`RSVP-SETUP.md`** (in the folder above this one, next to
`rsvp-apps-script.gs`). Short version:

1. Create a blank Google Sheet → **Extensions ▸ Apps Script** → paste in `rsvp-apps-script.gs`.
2. **Deploy ▸ New deployment ▸ Web app** → *Execute as: Me*, *Who has access: Anyone* →
   Deploy → authorize → copy the **Web app URL** (`https://script.google.com/macros/s/…/exec`).
3. In `index.html`, replace **`YOUR_GOOGLE_SCRIPT_URL`** (one spot) with that URL:

   ```powershell
   (Get-Content index.html -Raw) -replace 'YOUR_GOOGLE_SCRIPT_URL','https://script.google.com/macros/s/PASTE/exec' |
     Set-Content index.html -Encoding utf8 -NoNewline
   ```

4. Submit a test RSVP — a new row appears in your Sheet within seconds (and an email arrives).

Until the URL is set, the form politely tells guests to call/message instead, so it never
looks broken. Note: `rsvp-apps-script.gs` and `RSVP-SETUP.md` are **not** website files —
don't upload them to your host.

## How to publish (your own domain)

1. Upload **everything in this folder** to your site's web root (e.g. `public_html/`)
   via cPanel File Manager, FTP, or your host's deploy tool. Keep the folder structure
   (`assets/` must stay next to `index.html`).
2. Make sure hidden files are uploaded too — `.htaccess` is hidden by default in most
   FTP clients (enable "show hidden files").
3. If your host runs **IIS/Windows**, `web.config` is used automatically; you can delete
   `.htaccess`. On **Apache/LiteSpeed/cPanel**, `.htaccess` is used; you can delete `web.config`.
4. Visit your domain — done.

To publish under a subfolder (e.g. `example.com/wedding/`), upload the folder there; all
asset paths are relative, so it works without changes.

## Notes

- **Background music:** browsers block autoplay with sound until the visitor interacts.
  By design, the music starts when the guest taps the envelope to open the invitation, and
  the speaker button (bottom-right) toggles it.
- **Search engines:** this build is set to `noindex` (see `robots.txt` and the
  `<meta name="robots">` tag) so the invitation won't show up in Google. To make it
  publicly discoverable, set `robots.txt` to `Disallow:` (empty) and change the meta tag
  to `index, follow`.
- **External dependency:** the only thing loaded from the internet is **Google Fonts**.
  It degrades gracefully to system serif fonts if a guest is offline.
- **Optional further savings:** `music.mp3` is 7.3 MB. Re-encoding to ~128 kbps would cut
  it to ~2–3 MB. It only downloads when a guest opens the invitation, so this is optional.
