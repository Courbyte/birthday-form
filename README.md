# 🎂 Birthday Registration & Admin Dashboard

A simple birthday registration form with photo uploads, a login-protected admin dashboard, and an automated daily email alert — built with plain HTML/CSS/JS and [Supabase](https://supabase.com) (Postgres, Storage, Auth, Edge Functions). No backend server required; hosted as a static site.

## ✨ Features

- **Public registration form** (`index.html`) — name, email, phone, birthday, and an optional photo upload
- **Admin dashboard** (`admin.html`) — login-gated view of all registrations, sorted by nearest upcoming birthday, with search and delete
- **Daily automated email** — a Supabase Edge Function checks for today's birthdays (matching month/day, any year) and sends the admin one batched email with each person's name and photo
- **Secure by default** — Row Level Security policies mean the public form can only *insert* data, never read it; only an authenticated admin session can view or delete records

## 🧱 Tech stack

| Layer | Tool |
|---|---|
| Frontend | Static HTML, CSS, vanilla JS |
| Fonts / Icons | Google Fonts (Fredoka, Inter), Font Awesome |
| Database | Supabase (Postgres) |
| File storage | Supabase Storage (`avatars` bucket) |
| Admin auth | Supabase Auth (email/password) |
| Scheduled email | Supabase Edge Function + Gmail SMTP |
| Hosting | GitHub Pages |

## 📁 Project structure

```
├── index.html              # Public registration form
├── admin.html               # Login-protected admin dashboard
├── supabase-setup.sql       # Full DB schema, RLS policies, storage bucket & policies
└── send-birthday-emails.ts  # Edge Function: daily admin email via Gmail SMTP
```

## 🚀 Setup

1. **Database & storage** — run all of `supabase-setup.sql` once in the Supabase SQL editor. It creates the `birthdays` table, the `avatars` storage bucket, and every RLS policy needed.
2. **Admin login** — in Supabase Dashboard → Authentication → Users, add the admin's email/password. Then go to Authentication → Providers → Email and turn **off** "Allow new users to sign up."
3. **Frontend config** — in both `index.html` and `admin.html`, replace `SUPABASE_URL` and `SUPABASE_ANON_KEY` with your project's values (Settings → API). The anon key is safe to expose publicly; RLS policies are the real access control.
4. **Daily email function** — deploy `send-birthday-emails.ts` with the Supabase CLI:
   ```bash
   npx supabase functions deploy send-birthday-emails
   npx supabase secrets set GMAIL_USER=youraddress@gmail.com
   npx supabase secrets set GMAIL_APP_PASSWORD=your16charapppassword
   npx supabase secrets set ADMIN_EMAIL=admin@example.com
   ```
   Then schedule it (Edge Functions → Cron) to run daily, e.g. `0 6 * * *` for 7:00 AM WAT.

## 🌐 Deployment

This repo is a static site — no build step. Just enable **Settings → Pages → Deploy from a branch → main / (root)**, and it goes live at `https://<username>.github.io/<repo-name>/`. The admin dashboard is at `/admin.html`.

## 🔒 Security notes

- The Supabase anon key is meant to be public — it's not a secret.
- The public form can only insert rows and upload photos; it cannot read or delete anything.
- The admin dashboard requires a real Supabase Auth login; passwords are hashed by Supabase automatically.

## 👤 Credits

Built by **Azakaye Courage** — [Coding With Courbyte](https://github.com/Courbyte)
