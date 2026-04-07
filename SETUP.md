# Complete Supabase & Vercel Setup Guide

This guide walks you through setting up Brokerr from scratch with Supabase and deploying to Vercel.

## Table of Contents

1. [Supabase Setup](#supabase-setup)
2. [Resend Email Setup](#resend-email-setup)
3. [Local Development](#local-development)
4. [Vercel Deployment](#vercel-deployment)
5. [Post-Deployment](#post-deployment)

---

## Supabase Setup

### Step 1: Create Supabase Project

1. Go to [supabase.com](https://supabase.com)
2. Click "Start your project" or sign in
3. Click "New Project"
4. Fill in the form:
   - **Name**: `brokerr` (or your preferred name)
   - **Database Password**: Generate a strong password (save this!)
   - **Region**: Choose closest to your users (e.g., `us-east-1`)
5. Click "Create new project"
6. Wait 2-3 minutes for provisioning

### Step 2: Get Your Credentials

1. In your Supabase project, go to **Settings > API**
2. Copy these values:
   - **Project URL** → `VITE_SUPABASE_URL`
   - **Anon Key** (public key) → `VITE_SUPABASE_ANON_KEY`
   - Keep the **Service Role Key** secret (never use in frontend)

Your values will look like:
```
VITE_SUPABASE_URL=https://abcdef123456.supabase.co
VITE_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Step 3: Initialize Database Schema

1. In your Supabase project, go to **SQL Editor**
2. Click **"New Query"**
3. Copy the contents of `src/schema.sql` from this project
4. Paste into the SQL editor
5. Click **"Run"** (execute button)
6. Wait for completion (should see green checkmark)

The schema creates:
- `users` table
- `user_balances` table
- `portfolio` table
- `transactions` table
- `copy_trades` table
- `copy_traders` table
- `cryptocurrencies` table
- `email_notifications` table
- `platform_settings` table

Plus all RLS (Row-Level Security) policies for security.

### Step 4: Verify Row-Level Security

1. Go to **Authentication > Policies**
2. You should see policies for each table
3. Verify status shows "Enabled" for each table
4. If not enabled, click table name and enable RLS

RLS ensures users can only access their own data at the database level.

### Step 5: Create Default Admin User

1. Go to **SQL Editor** > **New Query**
2. Paste this query:
```sql
INSERT INTO users (email, role, created_at)
VALUES ('admin@brokerr.io', 'admin', NOW())
ON CONFLICT (email) DO NOTHING;

INSERT INTO user_balances (user_email, total_usd, available_usd, locked_usd)
VALUES ('admin@brokerr.io', 1000000, 1000000, 0)
ON CONFLICT (user_email) DO NOTHING;
```
3. Click **"Run"**

Then set the user's password:
1. Go to **Authentication > Users**
2. Find the admin user
3. Click the menu (•••) > Reset password
4. Email will be sent with reset link
5. Follow the link and set a strong password

### Step 6: Enable Email Confirmations (Optional)

1. Go to **Authentication > Email Templates**
2. Review the default templates
3. Customize if needed (change branding, links, etc.)
4. Save changes

---

## Resend Email Setup

### Step 1: Create Resend Account

1. Go to [resend.com](https://resend.com)
2. Click "Sign Up"
3. Enter your email
4. Verify your email address
5. Complete profile setup

### Step 2: Get API Key

1. In Resend dashboard, go to **API Keys**
2. Click **"Create API Key"**
3. Name it: `Brokerr Development`
4. Copy the key → `VITE_RESEND_API_KEY`
5. Key format: `re_xxxxxxxxxxxxxxxx`

### Step 3: Verify Sender Email

**Option A: Use Default Resend Email (Easiest)**
```
VITE_EMAIL_FROM=Brokerr <onboarding@resend.dev>
```
This works immediately but shows "onboarding@resend.dev"

**Option B: Use Your Domain (Recommended)**

1. In Resend, go to **Domains**
2. Click **"Add Domain"**
3. Enter your domain (e.g., `brokerr.io`)
4. Follow DNS setup instructions:
   - Copy the provided DNS records
   - Add them to your domain's DNS provider
   - Wait for verification (5-10 minutes)
5. Once verified, set:
```
VITE_EMAIL_FROM=Brokerr <noreply@brokerr.io>
```

**Option C: Use Gmail (Quick Testing)**
```
VITE_EMAIL_FROM=Brokerr <your-email@gmail.com>
```

### Step 4: Create Production API Key

For production (on Vercel):
1. Create another API key: `Brokerr Production`
2. Add to Vercel environment variables (see Vercel Deployment section)

---

## Local Development

### Step 1: Clone Repository

```bash
git clone https://github.com/yourusername/brokerr.git
cd brokerr
```

### Step 2: Install Dependencies

```bash
pnpm install
# or: npm install
```

### Step 3: Create Environment File

1. Copy the example file:
```bash
cp .env.example .env.development.local
```

2. Edit `.env.development.local`:
```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key-from-step-2
VITE_EMAIL_PROVIDER=resend
VITE_RESEND_API_KEY=re_your-key-from-step-2
VITE_EMAIL_FROM=Brokerr <noreply@brokerr.io>
VITE_ADMIN_EMAIL=admin@brokerr.io
```

3. Save the file

### Step 4: Start Development Server

```bash
pnpm dev
```

Output should show:
```
VITE v4.3.0 ready in 234 ms

➜  Local:   http://localhost:5173/
```

### Step 5: Open in Browser

1. Go to `http://localhost:5173`
2. Click "Sign Up"
3. Enter email: `admin@brokerr.io` and password
4. Confirm email (check your email inbox)
5. You're logged in!

### Step 6: Make Yourself Admin

1. Go to Supabase SQL Editor
2. Run:
```sql
UPDATE users SET role = 'admin' WHERE email = 'admin@brokerr.io';
```
3. Refresh the app
4. You should now see admin routes

### Troubleshooting Local Setup

**Problem: "VITE_SUPABASE_URL is not defined"**
- Solution: Restart dev server after editing `.env.development.local`

**Problem: "User not found"**
- Solution: Go to Supabase > Authentication > Users
- Create user manually or through sign-up flow

**Problem: "Not authorized to access table"**
- Solution: Check RLS policies in Supabase > Authentication > Policies
- Verify your user's email matches the query

**Problem: "Emails not sending"**
- Solution: Check `email_notifications` table in Supabase for status
- Verify API key is correct: `VITE_RESEND_API_KEY`
- Check Resend domain is verified

---

## Vercel Deployment

### Step 1: Push to GitHub

```bash
git add .
git commit -m "Initial commit"
git push origin main
```

### Step 2: Connect to Vercel

1. Go to [vercel.com](https://vercel.com)
2. Sign in with GitHub
3. Click "New Project"
4. Select your repository
5. Vercel auto-detects Vite settings
6. Click "Deploy"

Vercel will attempt deployment (it may fail due to missing env vars - that's OK).

### Step 3: Add Environment Variables

1. In Vercel project, go to **Settings**
2. Click **"Environment Variables"**
3. Add each variable:
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
   - `VITE_EMAIL_PROVIDER`
   - `VITE_RESEND_API_KEY`
   - `VITE_EMAIL_FROM`
   - `VITE_ADMIN_EMAIL`

4. For each variable:
   - Paste the value
   - Select "Production" (or "Preview" and "Production")
   - Click "Save"

### Step 4: Redeploy

1. Go to **Deployments**
2. Click the latest deployment
3. Click **"Redeploy"**
4. Wait for build completion (2-5 minutes)

### Step 5: Custom Domain (Optional)

1. Go to **Settings > Domains**
2. Click **"Add"**
3. Enter your domain
4. Follow DNS setup:
   - Copy the provided DNS record
   - Add to your domain provider
   - Wait 5-10 minutes for verification
5. Vercel auto-generates SSL certificate

Your app is now live at `your-domain.com` or `brokerr.vercel.app`

---

## Post-Deployment

### Step 1: Test Production

1. Go to your deployment URL
2. Test user signup/login
3. Test admin functions
4. Test email notifications:
   - Create deposit request
   - Check admin approves it
   - Verify email was received

### Step 2: Set Up Custom Domain (Resend)

If using your own domain for emails:

1. In Resend, go to **Domains**
2. Add your domain
3. Follow DNS verification
4. Update `.env` in Vercel:
```
VITE_EMAIL_FROM=Brokerr <noreply@yourdomain.com>
```
5. Redeploy

### Step 3: Configure Production Settings

In Supabase, set up production settings:

1. **Authentication**:
   - Go to Auth > Providers
   - Enable email provider (default)
   - Set redirect URLs to your production domain

2. **Security**:
   - Go to Security > SSL
   - Verify SSL is enabled (auto with Vercel)

3. **Backups**:
   - Go to Backups
   - Set up daily backups to S3 (optional but recommended)

### Step 4: Monitor in Production

Set up monitoring:

1. **Supabase Logs**:
   - Go to Logs > API Requests
   - Monitor for errors

2. **Vercel Analytics**:
   - Go to Analytics
   - Monitor page load times, errors

3. **Email Logs**:
   - In Supabase, check `email_notifications` table
   - Monitor delivery and bounce rates

### Step 5: Set Up Admin Alerts

1. In Supabase, go to **Database > Webhooks**
2. Create webhook for important events
3. Send alerts to Slack/Discord when:
   - Large transactions
   - Failed withdrawals
   - Multiple failed logins

---

## Environment Variables Reference

### Development (.env.development.local)

```env
# Supabase
VITE_SUPABASE_URL=https://project.supabase.co
VITE_SUPABASE_ANON_KEY=eyJhb...

# Email
VITE_EMAIL_PROVIDER=resend
VITE_RESEND_API_KEY=re_xxxxx
VITE_EMAIL_FROM=Brokerr <noreply@brokerr.io>

# Optional
VITE_ADMIN_EMAIL=admin@brokerr.io
```

### Production (Vercel Settings > Environment Variables)

Same as development but use production API keys.

---

## Common Deployment Issues

### Build Failed: "VITE_SUPABASE_URL is not defined"
- Solution: Add environment variables in Vercel Settings

### Build Success but App Not Loading
- Check browser console for errors
- Verify Supabase credentials are correct
- Check Supabase project is in good standing

### Emails Not Sending in Production
- Verify API key in Vercel matches your Resend key
- Check Resend domain is verified
- Check `email_notifications` table for status
- Monitor Resend dashboard for errors

### Users Can't Access Admin Pages
- Verify user role is set to 'admin' in Supabase
- Check that AuthContext properly detects role
- Test in incognito/private window

### Database Errors in Production
- Check RLS policies are enabled
- Verify policies allow your queries
- Monitor Supabase logs for errors
- Test queries manually in Supabase SQL Editor

---

## Scaling Checklist

When ready to scale:

- [ ] Supabase: Upgrade plan if approaching limits
- [ ] Vercel: Upgrade plan for faster builds
- [ ] Email: Increase Resend sending limits if needed
- [ ] Database: Add indexes on frequently queried columns
- [ ] Caching: Implement Redis for frequently accessed data
- [ ] CDN: Enable Vercel's Edge Network
- [ ] Monitoring: Set up error tracking (Sentry, LogRocket)
- [ ] Analytics: Track user behavior (PostHog, Mixpanel)

---

## Support

- **Documentation**: See README.md
- **Issues**: Open GitHub issue
- **Email**: support@brokerr.io
- **Supabase Docs**: [supabase.com/docs](https://supabase.com/docs)
- **Vercel Docs**: [vercel.com/docs](https://vercel.com/docs)

Happy deploying!
