# Google Maps No‑Website Lead Scraper (n8n + Apify) — Complete Step‑by‑Step Guide

This README explains exactly how to build an automation that finds businesses on Google Maps that DO NOT have websites — so they become potential website development clients.

The workflow uses:

* n8n (automation engine)
* Apify Google Maps Scraper Actor
* IF Node filter (final working solution)

---

## Goal

Automatically collect businesses from Google Maps and filter only those listings where the **website is missing**.

These businesses are high‑intent prospects for selling website development services.

---

## Final Working Logic (Important)

The entire problem came from over‑complicated conditions.

The correct working solution:

In the IF node:

Field: `website`
Operator: `is empty`
Value: *(leave blank)*

That’s it.

No regex
No expressions
No function nodes
No extra conditions

Because Apify removes the website field when it does not exist — so n8n treats it as empty automatically.

---

## Step 1 — Create Apify Actor

1. Go to [https://console.apify.com](https://console.apify.com)
2. Search for actor: **Google Maps Scraper**
3. Click → Try for free

Configure input:

Example search queries:

```
dentist in ghaziabad
coaching centre in delhi
gym near laxmi nagar
skin clinic in noida
```

Important settings:

| Setting            | Value  |
| ------------------ | ------ |
| Max places         | 50–200 |
| Include reviews    | OFF    |
| Include images     | OFF    |
| Include owner info | ON     |

Run the actor once to confirm output works.

---

## Step 2 — Connect Apify to n8n

In n8n:

1. Add node → **Apify**
2. Credentials → Add API token from Apify settings
3. Resource: Actor
4. Operation: Get Dataset Items
5. Dataset ID → copy from Apify run output

Execute node → you should see business listings.

---

## Step 3 — Understand the Data Problem

Apify returns inconsistent website data:

| Business    | website field                              |
| ----------- | ------------------------------------------ |
| Has website | "[https://clinic.com](https://clinic.com)" |
| No website  | field missing                              |

So instead of checking URL patterns, we simply check whether the field exists.

---

## Step 4 — Add IF Node (THE FIX)

Add an **IF node** after Apify.

Configure exactly like this:

### Condition

| Field     | Operator | Value           |
| --------- | -------- | --------------- |
| `website` | is empty | *(leave blank)* |

Do NOT add expressions.
Do NOT use regex.
Do NOT use contains.
Do NOT use undefined checks.

Just select from dropdown.

---

## Step 5 — Understand Branch Meaning

| Branch | Meaning                        |
| ------ | ------------------------------ |
| TRUE   | Business has NO website → Lead |
| FALSE  | Business already has website   |

We only keep TRUE branch.

---

## Step 6 — Store Leads

Add one of these after TRUE branch:

### Google Sheets Node

Fields to store:

* title
* phone
* address
* rating
* category

OR

### Airtable Node

Same mapping.

---

## Step 7 — Optional (Cold Outreach Ready)

You now have daily fresh prospects.
You can:

* Call them
* WhatsApp them
* Send demo website

---

## Final Workflow Structure

```
Apify → IF (website is empty) → Google Sheets
```

---

## Key Lesson Learned

The issue happened because of over‑engineering:

* Regex tried to detect real websites
* Expressions tried to detect undefined
* But Apify already removes the field

So the simplest check works best:

> If website field is empty → it’s a lead

---

## Result

You now automatically get daily businesses that need a website — perfect for selling web development services.
