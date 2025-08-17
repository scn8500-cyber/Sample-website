# Analytics Playground (GA4 + GTM) — Step-by-step

This repo gives you a tiny website you can host for free and wire up to **Google Analytics 4** using **Google Tag Manager**. No CS degree required, just mild patience.

## What you need
- A Google account
- A GitHub account

---

## Part A — Put the site online (GitHub Pages)

1) **Create a repo**
- Go to https://github.com/new
- Name it `analytics-playground` (or anything)
- Click **Create repository**

2) **Add the files**
- Upload `index.html` from this zip to the repo (or drag-and-drop)
- Commit the changes

3) **Enable GitHub Pages**
- In your repo: **Settings → Pages**
- **Source:** Deploy from a branch
- **Branch:** `main` and **/root**
- Click **Save**
- After a minute, your site will be live at a URL like: `https://<your-username>.github.io/<repo-name>/`

> Tip: Open that URL in a new tab and keep it handy.

---

## Part B — Create GA4 property and web data stream

1) Go to **analytics.google.com** → click **Admin** (gear) → **Create account** (if you don't have one)  
2) Under **Property**, click **Create property** → fill name and time zone  
3) In the property, click **Data streams** → **Web** → enter your site URL from Part A  
4) Copy the **Measurement ID** (looks like `G-XXXXXXX`)  
5) On the web data stream page, ensure **Enhanced measurement** is **On** (captures page_view, scroll, outbound_clicks, etc.)

You will NOT paste the `G-XXXXXXX` into the site directly. We'll send it via Tag Manager.

---

## Part C — Set up Google Tag Manager (GTM)

1) Go to **tagmanager.google.com** → Create **Account** and **Container**  
   - Account name: your company/site
   - Container name: same as site
   - Target platform: **Web**

2) Copy your **Container ID** (looks like `GTM-XXXXXXX`)

3) **Install GTM on your site**
   - Open `index.html` from this zip
   - Replace **all** `GTM-XXXXXXX` placeholders with your real container ID
   - Commit and deploy (push to GitHub so Pages redeploys)

4) **Verify installation**
   - In GTM, click **Preview** to open **Tag Assistant**
   - Enter your site URL, click **Connect**
   - A debug pane will appear on your site if GTM is installed correctly

---

## Part D — Send GA4 data via GTM

### 1) Add GA4 Configuration tag
- GTM → **Tags → New**
- **Tag Type:** *Google Analytics: GA4 Configuration*
- **Measurement ID:** your `G-XXXXXXX` from Part B
- **Trigger:** *All Pages*
- Save.

### 2) Add GA4 Event tags (custom events)

This site pushes events to `dataLayer`:
- `button_click` with params: `button_id`, `button_text`
- `video_play` with param: `video_title`
- `sign_up` with params: `method`, `email_domain`
- `outbound_click` with param: `link_url`

Create one tag per event (or one tag using variables; simple route below):

**a) Make a trigger for `button_click`**  
- GTM → **Triggers → New**
- **Trigger Type:** *Custom Event*
- **Event name:** `button_click`
- Save.

**b) Create GA4 Event tag for `button_click`**  
- **Tags → New → Google Analytics: GA4 Event**
- **Configuration tag:** select your GA4 Configuration (from step 1)
- **Event Name:** `button_click`
- **Event Parameters:**
  - `button_id` → *{{Variable}}* → *New Variable* → **Data Layer Variable**, name `button_id`
  - `button_text` → **Data Layer Variable** `button_text`
- **Trigger:** `button_click` (from step a)
- Save.

Repeat the same pattern for `video_play`, `sign_up`, and `outbound_click`:
- Make a **Custom Event trigger** with the event name
- Make a **GA4 Event tag** with matching Event Name
- Add parameters using **Data Layer Variable** names matching the payloads above (`video_title`, `method`, `email_domain`, `link_url`)

### 3) Optional: Click-based triggers
If you prefer to track clicks without using the provided `dataLayer.push()`:
- GTM → **Variables** → Enable built-ins: *Click ID, Click Text, Click Classes, Click URL*
- GTM → **Triggers → New → All Elements or Just Links**
- Set conditions like *Click Text equals "Click Button A"* and fire a GA4 Event tag.

### 4) Preview, then Publish
- Click **Preview** in GTM to test. The debug panel will show which tags fired.
- In **GA4 → Realtime**, you should see your events within seconds.
- When happy, click **Submit** in GTM to publish the container.

---

## Part E — Validate in GA4 (Realtime)

- GA4 → **Reports → Realtime**
- You should see your current user session and a list of events: `page_view`, `button_click`, `sign_up`, etc.
- Click into an event to view parameters (e.g. `button_id: A`).

---

## Troubleshooting

- **No data in Realtime:** Check that GTM is installed (Preview connects), and that the GA4 Configuration tag fires on All Pages.
- **Events not showing:** Confirm your Custom Event triggers match the exact event names (`button_click`, not `button-click`).
- **Outbound link not captured:** Ensure Enhanced Measurement is On, or create the `outbound_click` Custom Event tag as described.

---

## What next

- Standardize names using GA4 recommended events (e.g. `sign_up`, `purchase`).
- Add more params: campaign source, content group, user role, etc.
- Build simple dashboards in **Explore** to slice your events.

Happy measuring.
