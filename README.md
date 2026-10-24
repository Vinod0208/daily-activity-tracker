# Daily Activity Tracker

A React + Vite + Supabase starter for a small team to record daily activities and export monthly reports.

## Included

- Email/password sign-in and password reset flow
- User/admin roles (enforced with Supabase RLS)
- Default activities and private custom activities
- Daily multi-activity submissions with optional hours and remarks
- Personal activity history
- Admin user list and monthly detailed/summary CSV exports
- Responsive UI and Vercel SPA configuration

## Important current limitations

This is a starter implementation, not a verified production deployment. You must configure Supabase and test the policies with separate admin and user accounts before sharing it with your team. The starter records login events when a successful sign-in is observed in the app; it does not guarantee an exact count of unique login days if users sign in on multiple devices or never open the app after auth. Automated monthly email delivery and admin-side user creation UI are not included; use the Supabase dashboard for initial account provisioning. The monthly summary counts submitted days, not scheduled working days. This MVP does not infer attendance from login activity.

## 1. Create Supabase project

1. Create a project at https://supabase.com.
2. Open **SQL Editor** and run `supabase/schema.sql`.
3. In **Authentication → URL Configuration**, add your local URL `http://localhost:5173` and later your deployed Vercel URL to Site URL / Redirect URLs as appropriate.
4. Copy Project URL and the publishable/anon key from Project Settings → API.

## 2. Configure local environment

```bash
cp .env.example .env.local
```

Fill `.env.local`:

```env
VITE_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
VITE_SUPABASE_ANON_KEY=YOUR_PUBLISHABLE_OR_ANON_KEY
```

Never use the Supabase `service_role` key in a Vite variable or frontend code.

## 3. Create the first admin and users

For a small one-month pilot, provision users in Supabase Dashboard:

1. Open **Authentication → Users → Add user**. Create the admin with a strong password (or send an invite if email is configured).
2. Copy the new user's UUID and email.
3. In SQL Editor, insert a matching profile row. Replace values carefully:

```sql
insert into public.profiles (id, full_name, email, role, is_active)
values ('AUTH_USER_UUID_HERE', 'Admin Name', 'admin@example.com', 'admin', true);
```

Create regular users the same way but use `role = 'user'`.

**Security:** Do not let regular users access SQL Editor or Supabase project settings. Only trusted project owners should run admin SQL. Never expose the service-role key.

## 4. Run locally

```bash
npm install
npm run dev
```

Open http://localhost:5173.

## 5. Build

```bash
npm run build
```

## 6. Deploy to Vercel free tier

1. Put this folder in a private GitHub repository.
2. In Vercel, import the repository.
3. Set framework preset to Vite (or allow auto-detection).
4. Add `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` in Vercel Project Settings → Environment Variables.
5. Deploy.
6. Add the deployed URL to Supabase Auth URL Configuration and redeploy if necessary.

Free-tier terms and limits can change. Review current Vercel and Supabase limits before production use.

## Monthly CSV reports

As an admin, open **Monthly reports**, select a month and optional user, then download:
- User-wise summary CSV
- Detailed activity CSV

Exports include all rows loaded by the selected query, not only visible table rows.

## Suggested pre-launch checks

- Create separate admin and regular test accounts.
- Verify regular users cannot read another user's submissions.
- Verify regular users cannot alter their role.
- Verify an inactive user cannot access data.
- Verify CSV export totals against database rows.
- Verify password reset redirect after deployment.
- Confirm the month/timezone matches your team's reporting policy.
