# CA Study Pro

A mobile-first CA Foundation mentor/student learning platform starter.

## Included
- Separate Student and Mentor login/register screens.
- Mentor registration requires an invitation code (validated server-side by the signup trigger).
- Student registration requires a batch/group invitation code.
- Role-aware navigation and mentor-only task/calendar creation.
- Dashboard, tasks, academic calendar, mock test centre, group overview and analytics UI.
- Supabase PostgreSQL schema with row-level security policies.
- PWA manifest for installable Android/browser experience.

## Important status
This is a working frontend starter with demo data and the database foundation. It is **not yet a finished production system**. The next integration steps are real file uploads, mentor review screens, notifications, group creation/admin console, and replacing demo arrays with live Supabase queries.

## Requirements
- Node.js 20+
- A Supabase project

## Run locally
```bash
npm install
cp .env.example .env
```
Set these values in `.env`:
```env
VITE_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
VITE_SUPABASE_ANON_KEY=YOUR_SUPABASE_ANON_KEY
```
Then run:
```bash
npm run dev
```
Open the local URL printed by Vite.

## Configure Supabase
1. Create a Supabase project.
2. Open **SQL Editor** and run `supabase/schema.sql`.
3. In Authentication settings, enable email/password. For testing you may temporarily disable email confirmation; enable it before production.
4. Create private Storage buckets named `submissions` and `resources`.
5. Add storage policies before accepting real student uploads.
6. Create an initial admin using a secure one-time server-side process. Never allow public signup to select `admin`.
7. Create groups and student invitation codes using an admin-only server/SQL workflow.
8. Create mentor invitation codes by storing SHA-256 hashes, not plaintext. Example SQL for a one-use code:
```sql
insert into public.mentor_invite_codes(code_hash,label,max_uses,expires_at)
values (encode(digest('REPLACE-WITH-A-LONG-RANDOM-CODE','sha256'),'hex'),
        'First mentor',1,now()+interval '7 days');
```
9. Create a group and share its `invite_code` privately:
```sql
insert into public.study_groups(name,invite_code)
values ('CA Foundation Batch A','BATCH-A-CHANGE-THIS');
```

## Security notes
- Do not put the Supabase service-role key in frontend `.env` or commit it.
- Mentor access codes are checked in the database trigger. An invalid mentor code results in a student role, not mentor privileges.
- Before production, tighten profile updates and add admin-only Edge Functions for issuing codes, creating groups and promoting admins.
- Add private Storage RLS policies so students can upload only their own submissions and mentors can read only their assigned group's files.
- The included demo UI still uses local demo data for several views; connect the Supabase queries before real classroom use.

## Build
```bash
npm run build
```

## Suggested next milestones
1. Admin console: issue/revoke codes, create batches, assign mentors.
2. Real group membership and student directory.
3. Submission upload to private Supabase Storage.
4. Mentor evaluation: marks, annotations, feedback and resubmission.
5. Push/email reminders for tests and deadlines.
6. Calendar event editing, recurring events and Google Calendar export.
