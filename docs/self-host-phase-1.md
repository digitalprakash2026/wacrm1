# Self-hosted WA CRM — Phase 1 Delivery Plan

This Phase 1 plan is for selling **one dedicated WA CRM instance per business** instead of running a shared SaaS platform. Each customer should get their own deployment, Supabase project, Meta app/WABA connection, domain, secrets, and support handover.

## Phase 1 goal

Deliver a repeatable, low-risk, business-ready installation package:

1. Run the upstream template without regressions.
2. Deploy a demo/staging instance.
3. Connect one official Meta WhatsApp Cloud API number.
4. Verify inbox, contacts, broadcasts, automations, team roles, and settings.
5. Document the exact handover checklist for every customer.

## Recommended customer architecture

```text
Customer domain/subdomain
  -> Next.js deployment
  -> Supabase project for that customer
  -> Meta Developer App + WhatsApp Business Account
  -> One encrypted WhatsApp configuration for the customer's account
```

Use separate infrastructure per customer during the self-hosted phase. This keeps customer data isolated, simplifies legal/support boundaries, and avoids operating a central multi-tenant SaaS before the product is ready.

## What is already in the repo

- Next.js app with dev/build/start/lint/typecheck/test scripts.
- Supabase-backed auth, Postgres data model, and RLS migrations.
- Account/team sharing foundation with owner, admin, agent, and viewer roles.
- WhatsApp Cloud API configuration UI, token encryption flow, webhook callback, signature verification, templates, broadcasts, and inbox modules.
- Optional dry-run mode for template submission when a real WABA is not connected.

## Phase 1 scope

### Must ship before selling

- Clean local install and production build.
- One demo deployment on Vercel/Hostinger/Render plus one Supabase project.
- Meta test number connected end-to-end.
- Admin can configure WhatsApp credentials from settings.
- Incoming message creates/updates contact and conversation.
- Agent can reply from inbox.
- Team invitations and role changes tested.
- Contact import/export path validated.
- Broadcast template sync/send path validated.
- Automation builder smoke-tested.
- Backup and recovery steps documented.
- Client handover guide prepared.

### Do not build in Phase 1

- Central SaaS billing.
- Shared multi-customer control panel.
- Complex tenant-to-tenant migration tooling.
- Kubernetes or self-hosted Supabase unless a paying client specifically requires it.
- Heavy custom AI features before the core WhatsApp flow is reliable.

## Client setup checklist

Collect these from every customer before installation:

- Business name, logo, brand color, timezone, and preferred subdomain.
- Admin owner name and email.
- Team member names, emails, and roles.
- Meta Business Manager access.
- WhatsApp Business Account and phone number ownership.
- Permanent access token or system-user access for token generation.
- Phone Number ID, WABA ID, Meta App ID, and Meta App Secret.
- Approved message templates or template copy to submit.
- Existing contact CSV, if any.
- Sales pipeline stages and tags.

## Deployment checklist

1. Create a dedicated Supabase project.
2. Apply all migrations in order from `supabase/migrations`.
3. Configure auth redirect URLs for the customer domain.
4. Deploy the Next.js app.
5. Add environment variables:
   - `NEXT_PUBLIC_SUPABASE_URL`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `SUPABASE_SERVICE_ROLE_KEY`
   - `ENCRYPTION_KEY`
   - `META_APP_SECRET`
   - `NEXT_PUBLIC_SITE_URL`
   - `AUTOMATION_CRON_SECRET` if wait/scheduled automations are enabled
6. Create the first owner account.
7. Configure WhatsApp from the app settings screen.
8. Register and verify the Meta webhook.
9. Subscribe the Meta webhook to the `messages` field.
10. Send a test inbound and outbound WhatsApp message.
11. Invite the customer's team members.
12. Confirm role permissions with owner/admin/agent/viewer accounts.

## Acceptance tests for first paid delivery

Run these before handover:

- App loads on the customer domain over HTTPS.
- Owner can sign in and open dashboard, inbox, contacts, pipelines, broadcasts, automations, and settings.
- Admin can save WhatsApp configuration without exposing tokens to the browser database writes.
- Meta webhook verification succeeds.
- Meta webhook POSTs are rejected when the signature is missing/invalid.
- Incoming WhatsApp message appears in the inbox.
- Agent reply is sent through Meta Cloud API.
- Delivery/read/failed statuses update when Meta sends them.
- Contact tags and notes persist.
- Pipeline deal can be created and moved.
- Broadcast can be created with an approved template.
- Automation can be activated and triggered.
- Viewer cannot write operational data.
- Admin can invite/remove team members.
- A backup/export process is documented for the client.

## Support package recommendation

Sell Phase 1 as a done-for-you service, not only as software:

- Setup fee: installation, Meta configuration, branding, and training.
- Monthly support: uptime checks, WhatsApp troubleshooting, small fixes, backups, and campaign/automation help.
- Premium add-ons: custom reports, advanced automations, AI reply suggestions, lead scoring, multi-number setup, and custom hosting.

## Phase 1 exit criteria

Phase 1 is complete when we can install a fresh customer instance from scratch in under one working day and pass the acceptance tests above without code changes.
