# IAM Vault — Role Approval & Lifecycle Dashboard

A full-stack IAM dashboard for managing RBAC role approvals, tracking access lifecycle, and running half-yearly audits — powered by Supabase and hosted on Vercel.

> **Multi-admin ready** — all IAM admins share the same live database. Every change (log a role, revoke, audit decision) reflects instantly for the entire team.

---

## Live demo

**https://iam-vault.vercel.app** *(update this after deploying)*

---

## Stack

| Layer | Tool |
|---|---|
| Frontend | Vanilla HTML + CSS + JS |
| Database | Supabase (Postgres + REST API) |
| Hosting | Vercel (auto-deploys on every GitHub push) |

---

## Setup

### 1. Supabase — create tables

Go to your Supabase project → **SQL Editor** and run:

```sql
create table active_roles (
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

create table pending_approvals (
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

create table audit_queue (
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

create table revoked_roles (
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

create policy "Allow all" on active_roles for all using (true);
create policy "Allow all" on pending_approvals for all using (true);
create policy "Allow all" on audit_queue for all using (true);
create policy "Allow all" on revoked_roles for all using (true);
```

### 2. Add your Supabase keys

Open `config.js` and fill in your credentials:

```js
window.SUPABASE_URL = 'https://YOUR_PROJECT_ID.supabase.co';
window.SUPABASE_KEY = 'YOUR_ANON_PUBLIC_KEY';
```

Find these at: **Supabase Dashboard → Project Settings → API**

### 3. Deploy to Vercel

1. Push this repo to GitHub
2. Go to **vercel.com** → Import project → select `iam-vault`
3. No build settings needed — click **Deploy**
4. Done. Your URL: `https://iam-vault-[hash].vercel.app`

---

## Features

| Feature | Description |
|---|---|
| Active roles | Live registry of all approved access, filterable by dept / cost center / app |
| Pending approvals | Track requests with SLA breach alerts |
| Audit queue | Half-yearly recertification — Keep or Revoke per role |
| Revoked roles | Immutable audit trail |
| Log approved role | Form to log a new role from a Jira / ServiceNow ticket |
| Export CSV | Download active roles as spreadsheet |
| Real-time sync | Auto-refreshes every 30 seconds across all admin sessions |

---

## Approval workflow

```
Jira JSM / ServiceNow portal (user submits request)
        ↓
Line manager approves (email or portal)
        ↓
IAM admin gives final sign-off
        ↓
IAM admin provisions access in target system
        ↓
IAM admin logs approved role → this dashboard
        ↓
Half-yearly audit → Keep or Revoke
```

---

## Roadmap

- [ ] Jira API — auto-pull pending tickets into dashboard
- [ ] ServiceNow REST API — auto-populate approved records
- [ ] Supabase Auth — per-admin login and activity log
- [ ] Role catalog — job title to eligible roles mapping
- [ ] Email alerts for SLA breaches (Supabase Edge Functions)

---

Built with Claude (Anthropic).
