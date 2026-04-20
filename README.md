# IAM Vault — Role Approval & Lifecycle Dashboard

A lightweight, browser-based IAM dashboard for managing RBAC role approvals, tracking access lifecycle, and running half-yearly audits. Built for IAM Admin teams.

---

## What it does

| Feature | Description |
|---|---|
| **Active roles** | Full registry of all approved access records, filterable by department, cost center, and application |
| **Pending approvals** | Track requests awaiting line manager or IAM admin sign-off, with SLA breach indicators |
| **Audit queue** | Half-yearly / annual recertification review — IAM admin works through Keep or Revoke decisions |
| **Revoked roles** | Immutable audit trail of all removed access with reason and date |
| **Log approved role** | Structured form to log a new approval directly from a Jira / ServiceNow ticket |
| **Export CSV** | Download all active role records as a spreadsheet |
| **Persistent storage** | All data saved to localStorage — survives page refreshes |

---

## How the approval workflow fits in

This dashboard sits at the **end of the approval chain**:

```
User submits request via Jira JSM or ServiceNow portal
        ↓
Line manager approves (via portal or email)
        ↓
IAM admin reviews and gives final sign-off
        ↓
IAM admin provisions access in target system
        ↓
IAM admin logs the approved role into this dashboard
        ↓
Role sits in Active Roles until audit cycle triggers
        ↓
Half-yearly audit: IAM admin contacts manager → Keep or Revoke
```

---

## Role lifecycle states

```
Pending → Approved → Provisioned → Active → [Audit] → Recertified / Revoked
```

---

## Getting started

No install needed. Just open `index.html` in any modern browser.

```bash
git clone https://github.com/iam-0604/iam-vault.git
cd iam-vault
open index.html
```

---

## Live demo

**https://iam-0604.github.io/iam-vault/**

---

## Fields logged per role record

| Field | Example |
|---|---|
| User name | John Smith |
| Employee email | john.smith@company.com |
| Job title | Finance Analyst |
| Department | Finance |
| Cost center | CC-1020 |
| Application | SAP |
| Role approved | SAP Finance Read |
| Access level | Read |
| Date approved | 15 Jan 2025 |
| Next audit due | 15 Jul 2025 |
| Ticket reference | JIRA-4521 |
| Line manager | Jane Doe |
| Approval notes | Free text from email thread |

---

## Tech stack

- Pure HTML + CSS + JavaScript — zero dependencies
- localStorage for persistence
- No backend required
- GitHub Pages compatible

---

## Roadmap

- [ ] Jira API integration — auto-pull pending tickets
- [ ] ServiceNow REST API — auto-populate approved records
- [ ] Multi-admin shared database (Supabase)
- [ ] Role catalog — job title to eligible roles mapping
- [ ] Email notifications for SLA breaches

---

Built using Claude (Anthropic) as part of an IAM tooling initiative.
