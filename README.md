# IAM Vault

IAM Vault is a lightweight IAM dashboard for managing role approvals, active access, audit reviews, and revoked roles. It uses a static frontend with Supabase as the backend and is deployed on Vercel.

Live app: [https://iam-vault00.vercel.app](https://iam-vault00.vercel.app)

## Features

- Track active role assignments
- Log and review pending approvals
- Run periodic access audits
- Record revoked access with an audit trail
- Export active role data as CSV
- Share one live Supabase-backed dataset across admin users

## Tech Stack

- Frontend: HTML, CSS, JavaScript
- Database: Supabase Postgres + REST API
- Hosting: Vercel

## Project Files

- `index.html` - app UI and client logic
- `config.js` - Supabase project URL and anon key
- `vercel.json` - static deployment settings
- `README-supabase.md` - extra setup reference

## Setup

### 1. Create the Supabase tables

Run this in the Supabase SQL Editor:

```sql
create extension if not exists pgcrypto;

create table if not exists active_roles (
  id uuid default gen_random_uuid() primary key,
  user_name text not null,
  email text not null,
  job_title text not null,
  department text not null,
  cost_center text not null,
  application text not null,
  role_name text not null,
  access_level text,
  approved_date date not null,
  next_audit_date date,
  ticket_ref text not null,
  line_manager text,
  notes text,
  created_at timestamptz default now()
);

create table if not exists pending_approvals (
  id uuid default gen_random_uuid() primary key,
  request_id text not null,
  user_name text not null,
  role_name text not null,
  application text not null,
  submitted_date date not null,
  awaiting text not null,
  sla_date date not null,
  created_at timestamptz default now()
);

create table if not exists audit_queue (
  id uuid default gen_random_uuid() primary key,
  user_name text not null,
  role_name text not null,
  application text not null,
  last_certified date,
  due_date date,
  manager text,
  manager_response text default 'Pending',
  decision text default 'pending',
  created_at timestamptz default now()
);

create table if not exists revoked_roles (
  id uuid default gen_random_uuid() primary key,
  user_name text not null,
  role_name text not null,
  application text not null,
  approved_date date,
  revoked_date date not null,
  reason text,
  revoked_by text default 'IAM Admin',
  created_at timestamptz default now()
);

alter table active_roles enable row level security;
alter table pending_approvals enable row level security;
alter table audit_queue enable row level security;
alter table revoked_roles enable row level security;

drop policy if exists "Allow all" on active_roles;
drop policy if exists "Allow all" on pending_approvals;
drop policy if exists "Allow all" on audit_queue;
drop policy if exists "Allow all" on revoked_roles;

create policy "Allow all" on active_roles for all using (true) with check (true);
create policy "Allow all" on pending_approvals for all using (true) with check (true);
create policy "Allow all" on audit_queue for all using (true) with check (true);
create policy "Allow all" on revoked_roles for all using (true) with check (true);
```

### 2. Configure Supabase

Update `config.js` with your own project values:

```js
window.SUPABASE_URL = 'https://your-project-id.supabase.co';
window.SUPABASE_KEY = 'your-anon-public-key';
```

Use the `anon` key from `Project Settings -> API`. Do not use the `service_role` key in a browser app.

### 3. Deploy to Vercel

1. Push the repo to GitHub.
2. Import the repo into Vercel.
3. Use the `Other` framework preset.
4. Deploy with the repo root as-is.

## Notes

- If the app shows `Supabase not configured`, check `config.js`.
- If the site returns `404`, make sure the entry file is named `index.html`.
- If data fails to load, verify the tables and RLS policies were created in the correct Supabase project.

## Roadmap

- Add authentication for IAM admins
- Add Jira or ServiceNow integration
- Add role catalog support
- Add automated audit reminders
