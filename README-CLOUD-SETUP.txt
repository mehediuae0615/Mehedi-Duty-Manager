MEHEDI DUTY MANAGER — CLOUD PIN SYNC PACKAGE
Prepared: 26 Nov 2026

FILES
- index.html: Duty Manager with PIN login/register and cloud month sync; PDF prints Restaurant and Section in separate columns.
- index.ts: Supabase Edge Function source (smooth-action).
- 20261126_duty_manager_cloud.sql: one-time schema/RPC migration + invitation manager function.
- config.toml: function JWT gateway setting.

IMPORTANT: Cloud requires ONE-TIME changes inside your Supabase project. I cannot log into or deploy to your Supabase account from this chat. Do not share secret values in chat.

ONE-TIME SUPABASE SETUP
1) Supabase Dashboard > SQL Editor > New query. Open the provided SQL file, copy all SQL, paste, and Run. If any SQL error occurs, stop and send the exact error; do not continue with a partial migration.
2) Edge Functions > smooth-action > Code. Replace the full index.ts with the provided index.ts. Save/Deploy only after the SQL has completed successfully.
3) In function settings, turn Verify JWT OFF for smooth-action. This is required for PIN-only custom sessions; the function verifies its signed 8-hour session token on every protected request. Do not disable token verification inside the code.
4) Add/confirm function secrets:
   - SUPABASE_SECRET_KEYS: JSON object containing the server-only secret key under property SUPABASE_SERVICE_ROLE_KEY. Use your project’s server secret key value. Never put this value in index.html or send it to anyone.
   - EMPLOYEE_SESSION_SECRET: a random secret at least 32 characters/bytes. Generate a new random value; do not use a PIN or a memorable phrase.
   - CORS_ALLOWED_ORIGINS: https://mehediuae0615.github.io
   SUPABASE_URL is normally supplied by Supabase automatically. If it is not, set it to https://lnnvtspahdprvcjcoiur.supabase.co
5) Deploy function smooth-action. Confirm it returns a JSON error/response, not “Hello Functions!”.
6) Manager creates one invitation per coworker in SQL Editor by running (replace exact employee name):
   select * from public.create_duty_invitation('Worker Full Name', 7);
   Privately send that employee the returned 64-character invitation token. It can be used once within 7 days. Employees register their exact invited name + their own 6–12 digit PIN.
7) Upload/replace index.html in the GitHub Pages repository. PIN Login will then pull and save monthly duty records to Supabase.

DATA / SECURITY NOTES
- PIN is hashed with PBKDF2 (310,000 iterations) and a random salt.
- The invitation is one-time and name-bound.
- Custom session is signed HS256, expires after 8 hours, and employee status is checked on every protected request.
- Records are filtered by authenticated employee_id server-side. RLS stays enabled with no public direct-access policies.
- Login attempts are rate-limited in a private table using an atomic RPC (8 attempts / 15 minutes per normalized employee-name bucket).
- Clearing Safari website data removes the local session; sign in again with PIN. Cloud duty rows remain in Supabase.
- The app also keeps local browser save and backup/restore.
- Do not use Private Browsing for reliable local autosave. Cloud sync requires normal Safari and network access.
