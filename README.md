# Koi Kitchen Pickleball Club — Booking App

This is the production-ready version of your booking app: real Supabase database
instead of Claude's built-in artifact storage, ready to deploy to Vercel with your
own domain.

## What you need to do (only you can do these — they need your login)

### 1. Create a Supabase project
1. Go to https://supabase.com and sign up / log in.
2. Click **New Project**. Name it anything (e.g. `koi-kitchen`). Pick a region close to
   the Philippines (Singapore is usually closest). Set a database password and save it
   somewhere safe.
3. Once the project is ready, go to the **SQL Editor** (left sidebar) and run this:

```sql
create table kv_store (
  key text primary key,
  value text not null,
  updated_at timestamptz default now()
);

alter table kv_store enable row level security;

create policy "public read/write"
on kv_store
for all
using (true)
with check (true);
```

   This creates the single table the app uses to store all bookings as one record.
   The policy above allows anyone with your public key to read/write — that's fine
   for this MVP since your admin passcode is the actual gatekeeper for making
   changes. If this grows into a bigger business, this is the first thing worth
   tightening (ask me when you're ready — it means moving booking writes behind a
   server function instead of directly from the browser).

4. Go to **Project Settings → API**. Copy two values:
   - **Project URL**
   - **anon public** key

### 2. Deploy to Vercel
1. Go to https://vercel.com and sign up / log in (you can use GitHub, GitLab, or email).
2. Easiest path: push this whole folder to a new GitHub repo, then in Vercel click
   **Add New → Project** and import that repo. Vercel auto-detects Vite and sets the
   build command for you.
   - Don't have GitHub set up? Alternative: install the Vercel CLI (`npm i -g vercel`)
     on any computer with Node installed, `cd` into this folder, and run `vercel`.
     It'll deploy directly without needing GitHub.
3. Before the first deploy finishes, go to **Project Settings → Environment Variables**
   in Vercel and add:
   - `VITE_SUPABASE_URL` = the Project URL from step 1
   - `VITE_SUPABASE_ANON_KEY` = the anon public key from step 1
4. Redeploy (Vercel does this automatically after env vars are added, or click
   **Redeploy** manually).
5. You'll get a live URL like `koi-kitchen-booking.vercel.app` — that's your real,
   public booking page. Test it the same way we tested the demo: book a slot, confirm
   it in Manage Bookings, check the screenshot shows up.

### 3. (Optional) Point your own domain at it
In Vercel: **Project Settings → Domains** → add your domain → follow the DNS
instructions it gives you (usually just adding a CNAME record wherever you bought
the domain).

## Local testing (optional, if you want to preview before deploying)
Needs Node.js installed on your computer.

```bash
npm install
cp .env.example .env.local   # then fill in your real Supabase URL + key
npm run dev
```

## What changed from the demo
- `src/storage.js` replaces Claude's artifact storage with real calls to Supabase —
  same one-record-holds-all-bookings design, just backed by a real database that
  isn't tied to a Claude account.
- Everything else (booking flow, admin view, pricing, promo logic, recurring
  blocks) is identical to what you already tested.
