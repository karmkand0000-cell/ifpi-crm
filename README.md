# IFPI CRM

Lead and admissions CRM for **IFPI**, an institute teaching NISM certifications (Mutual Fund V-A, Equity Derivatives VIII, Research Analyst XV, Investment Adviser X-A/X-B and more) and stock market courses.

It tracks every enquiry from first message to admission and fee collection, with one-tap WhatsApp follow-ups.

## Features

| Area | What it does |
|---|---|
| **Dashboard** | Overdue follow-ups, new leads, admissions and fees collected this month, conversion rate, 30-day lead trend, pipeline, lead sources, top courses, counsellor performance |
| **Follow-ups** | Overdue / today / next 7 days, with WhatsApp, Call and “Done, +2 days” buttons |
| **Leads** | Search and filter by stage, course, source, counsellor, priority. Bulk assign, bulk stage change, CSV import and export |
| **Lead profile** | Activity timeline (calls, notes, WhatsApp, stage changes), full details, WhatsApp templates filled in with the lead's name/course/batch, fees and payments |
| **Pipeline** | Drag-and-drop board: New → Contacted → Interested → Demo Scheduled → Demo Attended → Fee Discussion → Enrolled / Lost |
| **Students** | Enrolled students by course and batch, fee, paid, balance, one-tap fee reminder |
| **Payments** | Fee receipts by date and mode (UPI, cash, card, EMI…), CSV export |
| **Courses & batches** | NISM and stock market courses pre-loaded; add fees, batches, seats |
| **Message templates** | Editable WhatsApp messages with `{name}`, `{course}`, `{batch}`, `{date}`, `{balance}` placeholders |
| **Team** | Admin approves counsellors and sets roles. Counsellors only see their own and unassigned leads |
| **Public enquiry form** | `enquiry.html` — share in Instagram bio, ads, website. Leads land in the CRM tagged with source and campaign |

Built as a plain HTML/CSS/JavaScript app (no build step) on **Supabase** (Postgres database + logins), hosted free on **GitHub Pages**.

---

## Setup (about 20 minutes, no coding)

### 1. Put the code on GitHub

1. Sign in at [github.com](https://github.com) and click **New repository**. Name it `ifpi-crm`, keep it **Public** (required for free GitHub Pages), click **Create repository**.
2. On the new repo page click **uploading an existing file**.
3. Unzip `ifpi-crm.zip` on your computer, open the folder, select **everything inside it** (including the `css`, `js`, `assets`, `supabase` folders) and drag it into the upload box. Click **Commit changes**.
4. Go to **Settings → Pages**. Under *Build and deployment* choose **Deploy from a branch**, branch **main**, folder **/ (root)**, then **Save**.
5. After 1–2 minutes your CRM is live at `https://YOUR-USERNAME.github.io/ifpi-crm/`.

It opens in **demo mode** with sample leads, so you can click around straight away. Demo data is saved only in that browser.

### 2. Create the database (Supabase)

1. Sign up free at [supabase.com](https://supabase.com) → **New project**. Pick region **Mumbai (ap-south-1)**, set a database password, create.
2. Open **SQL Editor → New query**. Open `supabase/schema.sql` from this repo, copy all of it, paste, click **Run**. You should see “Success”.
3. Open **Authentication → URL Configuration**. Set **Site URL** to your GitHub Pages address (`https://YOUR-USERNAME.github.io/ifpi-crm/`) and save. This makes email confirmation and password-reset links open your CRM.
4. Open **Project Settings → API**. Copy the **Project URL** and the **anon public** key.

### 3. Connect the app to the database

1. In your GitHub repo open `js/config.js` and click the pencil (Edit) icon.
2. Paste the two values:
   ```js
   SUPABASE_URL: "https://abcdxyz.supabase.co",
   SUPABASE_ANON_KEY: "eyJhbGciOi...",
   ```
3. Click **Commit changes**. Wait a minute for GitHub Pages to update, then reload the CRM.

The anon key is designed to be public. All data is protected by row-level security rules in `schema.sql`.

### 4. Create the admin and add your team

1. Open the CRM → **Create an account**. **The first account becomes the admin.**
2. If Supabase asks you to confirm your email, click the link in your inbox, then sign in.
3. Send the CRM link to each counsellor. They choose **Create an account** and appear in **Team** as *waiting for approval*. Click **Approve**.
4. Go to **Courses & batches** and enter your fees and upcoming batches.

---

## Daily use

- **Start the day on Follow-ups.** Clear overdue first. “Done, +2d” logs the call and schedules the next one.
- **WhatsApp button** opens WhatsApp (app or web) with the template already typed. Press send in WhatsApp. The CRM logs it and moves a New lead to Contacted.
- **Enrolling a student:** change stage to *Enrolled* → choose batch and final fee → record the payment in *Fees & payments*.
- **Lost leads:** you'll be asked for a reason, which shows why enquiries don't convert.

## Enquiry form links

Share different links per channel so the dashboard shows where leads come from:

```
https://YOUR-USERNAME.github.io/ifpi-crm/enquiry.html?source=Instagram
https://YOUR-USERNAME.github.io/ifpi-crm/enquiry.html?source=Facebook&campaign=nism-viii-oct
https://YOUR-USERNAME.github.io/ifpi-crm/enquiry.html?source=YouTube&course=NISM-VIII
```

`source` must match one of the sources in `js/config.js`. `course` accepts a course code (e.g. `NISM-V-A`) to pre-select it.

## Importing an existing list

**Leads → Import.** Upload a CSV or paste from Excel / Google Sheets with a header row. Recognised columns: `name, phone, email, city, course, source, campaign, notes`. Phone numbers already in the CRM are skipped.

## Customising

Everything institute-specific is in `js/config.js`: name, tagline, pipeline stages, lead sources, lost reasons, payment modes. Colours and fonts are at the top of `css/styles.css`.

## Security notes

- New sign-ups are **inactive until an admin approves them**.
- Counsellors see only leads assigned to them plus unassigned leads; admins see everything.
- Only admins can delete leads or payments, edit courses, or change roles.
- The public enquiry form can only *add* a new lead. It cannot read any data.
- To stop public sign-ups entirely, turn off **Authentication → Providers → Email → Allow new users to sign up** in Supabase after your team has joined, and add people from **Authentication → Users → Add user** instead.

## Good to know

- Supabase's free plan may pause a project after a period of inactivity. Check Supabase's current plan limits, and upgrade if the CRM becomes business-critical.
- Back up regularly with **Leads → Export CSV** and **Payments → Export CSV**, or from Supabase → Database → Backups on a paid plan.
- WhatsApp messages are sent by the counsellor from their own WhatsApp. Fully automatic WhatsApp messages need the WhatsApp Business API (Meta Cloud API or a provider such as Interakt/AiSensy/WATI) and can later be connected to this same Supabase database, e.g. with n8n.

## Project structure

```
index.html          CRM app
enquiry.html        Public enquiry form
css/styles.css      Styles
js/config.js        Settings + Supabase keys  ← edit this
js/db.js            Data layer (Supabase, or demo mode)
js/state.js         Shared state and business rules
js/ui.js            UI helpers
js/app.js           Sign-in, navigation, routing
js/views/*.js       One file per screen
supabase/schema.sql Database tables, security rules, starter courses and templates
```
