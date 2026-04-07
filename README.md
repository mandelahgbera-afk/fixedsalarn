# Brokerr — Crypto Copy-Trading Platform

A full-stack cryptocurrency copy-trading platform built with React, Vite, and Supabase.

## Features

- **User Dashboard** — Portfolio overview, balance tracking, P&L charts, allocation donut
- **Trade** — Buy/sell cryptocurrencies with real-time pricing
- **Copy Trading** — Browse and copy professional traders' strategies
- **Transactions** — Deposit/withdrawal requests with admin approval flow
- **Portfolio** — Detailed holdings breakdown with cost basis tracking
- **Settings** — Profile management

### Admin Panel

- **Admin Dashboard** — Platform stats, pending approvals, deposits vs withdrawals charts
- **Manage Users** — View all users, toggle admin roles, suspend/reactivate accounts
- **Manage Cryptos** — Add/edit/toggle cryptocurrencies available for trading
- **Copy Traders** — CRUD management of copy traders shown to users
- **All Transactions** — Review, approve, or reject pending deposits/withdrawals
- **Platform Settings** — Manage deposit wallet addresses (super admin only)

## Tech Stack

- **Frontend:** React 18, Vite, TailwindCSS, Framer Motion, Recharts, Lucide Icons
- **Backend/DB:** Supabase (PostgreSQL + Auth + Row Level Security)
- **Routing:** React Router v6 with nested layouts
- **Notifications:** Sonner toast notifications

## Environment Variables

Set these in your Vercel project settings or a local `.env` file:

| Variable | Description |
|---|---|
| `VITE_SUPABASE_URL` | Your Supabase project URL (`https://xxx.supabase.co`) |
| `VITE_SUPABASE_ANON_KEY` | Your Supabase anonymous/public key |
| `VITE_ADMIN_EMAIL` | Email address for the default admin user |

## Database Setup

Run the SQL in `src/schema.sql` in your Supabase SQL Editor to create all required tables:

- `users`, `user_balances`, `cryptocurrencies`, `portfolio`
- `transactions`, `copy_traders`, `copy_trades`
- `platform_settings`, `email_notifications`

## Deploying on Vercel

1. Push this folder to a GitHub repository
2. Import the repository in [vercel.com](https://vercel.com)
3. Set the three environment variables in Vercel project settings
4. Deploy — Vercel will auto-detect Vite and build `dist/`

The included `vercel.json` handles SPA routing so React Router works on page refresh.

## Running Locally

```bash
cp .env.example .env.local   # fill in your Supabase credentials
npm install
npm run dev
```

## Project Structure

```
src/
├── api/
│   ├── dbClient.js          # Supabase entity CRUD adapter
│   ├── supabaseClient.js    # Supabase client + auth helpers
│   └── emailService.js      # Email notification service
├── components/
│   ├── layout/              # AppLayout, Sidebar, MobileBottomNav
│   └── ui/                  # Button, Input, PageHeader, CryptoRow, etc.
├── lib/
│   ├── AuthContext.jsx      # Auth provider with login/logout/register
│   └── emailTemplates.js    # HTML email templates
├── pages/
│   ├── admin/               # AdminDashboard, ManageUsers, ManageTraders, etc.
│   ├── Dashboard.jsx
│   ├── Portfolio.jsx
│   ├── Trade.jsx
│   ├── CopyTrading.jsx
│   ├── Transactions.jsx
│   └── Settings.jsx
├── App.jsx                  # Router configuration
├── main.jsx                 # Entry point
└── schema.sql               # Full database schema
```

## Admin Access

Set a user's `role` column to `"admin"` in the Supabase `users` table. Admin users automatically see the admin panel navigation.

## Authentication

```javascript
import { useAuth } from '@/lib/AuthContext';
const { user, isAdmin, isAuthenticated, login, logout, register } = useAuth();
```
