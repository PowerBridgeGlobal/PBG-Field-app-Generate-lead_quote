# PBG SOP Gita — the living handbook

PBG SOP Gita is PowerBridge Global's daily handbook: each person's role, daily
checklist and the SOPs that apply to them, in **English and Telugu**. It runs
on the same stack as the rest of PBG — a Google Apps Script backend with a
Google Sheet for data, and a lightweight web app on GitHub Pages.

This folder holds two things:

- `backend/` — the Geeta Apps Script (a **separate** script from PB1 Core,
  with its own sheet `PBG_Geeta`).
- (coming next) the standalone handbook app that staff open on their phones.

---

## Stage A — what's built so far

The data structure (the `PBG_Geeta` sheet) and the **read endpoints** with
server-side permission checks:

- A normal person can read **only their own role's** material.
- **Founder / Admin / Satish** can read all roles and SOPs.
- **Pricing** is reserved for Founder / Satish.
- The attendance **grace value is never read or shown** by any endpoint.
- Only SOPs marked **Approved** are served.

Nothing is deleted anywhere — edits archive the old version.

---

## One-time setup (about 10 minutes, no developer needed)

1. Go to **script.google.com → New project**. Name it **PBG Geeta**.
2. For each file in `backend/` (`Config.gs`, `Lib.gs`, `Bootstrap.gs`,
   `Api.gs`): in the Apps Script editor click **+ → Script**, name it the same,
   and paste the file's contents. (Delete the default `Code.gs`.)
3. Click **Run ▸ setupGeeta** once. Approve the permissions prompt.
   This creates the **PBG_Geeta** sheet in your Drive, builds all the tabs,
   and seeds the 7 roles. Open **View ▸ Logs** to see the new sheet's link.
4. **Deploy ▸ New deployment ▸ Web app.**
   - *Execute as:* **Me**
   - *Who has access:* **Anyone**
   - Click **Deploy** and copy the **Web app URL** (ends in `/exec`).
   Remember: after any code change you must **Deploy ▸ Manage deployments ▸
   New version** for it to go live.

> To test before the real PIN login exists, open **Project Settings ▸ Script
> Properties** and add `GEETA_ALLOW_DEV_LOGIN` = `TRUE`. Turn it off (delete it)
> before staff use the app.

---

## How to test Stage A (in a phone or computer browser)

Paste your `/exec` URL, then add each test on the end:

1. **It's alive:**
   `…/exec?action=ping`
   → `{"ok":true,"data":{"app":"PBG Geeta",...}}`

2. **Sign in as the Store Helper (test mode):**
   `…/exec?action=devLogin&role_id=ARJUN`
   → copy the `token` from the reply.

3. **See the Store Helper's role** (paste the token):
   `…/exec?action=getRole&role_id=ARJUN&token=PASTE_TOKEN`
   → you'll get Arjun's role row + an (empty for now) checklist and SOP list.
   The content is empty on purpose until the v1.1 text is loaded.

4. **Permission check — a helper may not see another role:**
   `…/exec?action=getRole&role_id=SATISH&token=PASTE_TOKEN`
   → `{"ok":false,"error":"You can only view your own role."}` (and the attempt
   is logged in the **AccessDenials** tab).

5. **Founder sees everything:**
   `…/exec?action=devLogin&role_id=FOUNDER` → use that token with
   `action=listRoles` and `action=getRole&role_id=SATISH`.

If all five behave as above, Stage A passes and we move to the next stage.
