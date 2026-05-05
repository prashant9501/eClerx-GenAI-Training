# AWS OpenSearch Serverless — Trainee Setup Guide

This guide walks you through the **one-time AWS setup** you need before working with Amazon OpenSearch Serverless (OSS). Without these steps, you will hit `403 security_exception` errors when trying to create indices, query collections, or use the OpenSearch Dashboards UI.

**You only need to do this once per AWS account.** Once the steps below are complete, every lab and the Capstone that touches OSS will work without further setup.

---

## Why this exists (the two-layer auth model)

Amazon OpenSearch Serverless has **two separate authorization layers** that BOTH have to grant you access. This is unusual — most AWS services only have IAM. Get one wrong and you'll be confused for an hour.

| Layer | What it controls | Where set | Symptom on deny |
|---|---|---|---|
| **IAM permissions** (`aoss:*` actions) | Whether you can call the OSS service API at all | Your IAM user / group (already attached for you via the **Trainees** IAM group) | `User ... is not authorized to perform: aoss:...` |
| **Data Access Policy (DAP)** | Whether you can read / write / admin actual indices INSIDE a collection | OSS console — a separate resource per collection | `no permissions for [indices:admin/...]` or `Access denied when checking existence of index ...` |

**The key insight:** even with full IAM permissions (or even `AdministratorAccess`), you will still get **403 errors from the OSS data plane** until you create a Data Access Policy that lists you as a Principal. AWS does not ship a managed Data Access Policy — every user has to explicitly create one for themselves (or have one created on their behalf).

This guide is the self-service path: **you create your own DAP in 3 clicks**, with no instructor involvement.

---

## When you need this

| Module / Lab | Touches OSS? | Why |
|---|---|---|
| **Lab 6.1 — Bedrock Production RAG** | ✅ **Directly** | Uses `opensearch-py` to create indices, define `knn_vector` mappings, bulk-insert documents, run similarity searches. Every operation hits the DAP. |
| **Lab 4.4 — Bedrock Knowledge Bases** | ⚠ Indirectly | Bedrock KB internally uses OSS as its vector store. The Bedrock service has its own auto-created DAP, but YOU need a DAP to inspect / debug the KB's index manually. |
| **Capstone — Email Triage Agent** | ⚠ Indirectly | Same as Lab 4.4 — uses Bedrock KB; the DAP only matters if you want to debug retrieval directly. |
| All other labs | ❌ No | Use FAISS or ChromaDB; no AWS-side index work. |

If you only ever do Lab 4.4 and the Capstone, the DAP is optional (but recommended). For Lab 6.1, **the DAP is required** before the first index-creation cell will succeed.

---

## Step 1 — IAM permissions (already done for you)

This is here for context only — your **Trainees** IAM group already has the right permissions attached. You don't need to do anything for Step 1; it's described here so you understand what's been granted.

The Trainees group has these AWS-managed and inline policies:

- **`AmazonBedrockFullAccess`** (managed) — `bedrock:*` for KB / Guardrail / agent operations
- **Inline `BedrockKBVectorStoreAccess`** — `aoss:*` and `s3vectors:*` for the Bedrock KB wizard's underlying vector-store provisioning
- Plus the standard ones (S3, Comprehend, Textract, etc.)

If you want to verify: AWS Console → **IAM → Users → your username → Permissions tab** → you should see the Trainees group attached.

---

## Step 2 — Create your Data Access Policy (do this once)

This is the part you have to do yourself. Roughly 3 minutes total.

### 2.1 Find your IAM user ARN

You need your full ARN, which looks like `arn:aws:iam::345804339279:user/<YOUR-USERNAME>`.

**Option A — From the AWS Console (easiest):**

1. AWS Console → **IAM → Users** → click your own username
2. The "Summary" panel at the top shows your **User ARN** — copy it

**Option B — From the AWS CLI:**

```bash
aws sts get-caller-identity
```

The `Arn` field in the response is what you want.

### 2.2 Create the Data Access Policy

1. AWS Console → **Amazon OpenSearch Service**
2. In the left navigation, under **Serverless**, click **Data access policies**
3. Click **Create access policy** (top right)
4. Fill in:
   - **Name:** `<your-username>-fullaccess` — for example, `harshita-fullaccess` or `prashant-fullaccess`. Names must be unique within the AWS account, so use your username as a prefix.
   - **Description:** `Full OSS data-plane access for <your-username>` (or anything — optional)
   - **Apply policy to:** leave the default (Policy details defines via JSON)
5. **Define policy** — click the **JSON** tab (not the Visual editor) and paste exactly this — only change `<YOUR-USERNAME>` to your own IAM username:

```json
[
  {
    "Rules": [
      {
        "ResourceType": "collection",
        "Resource": ["collection/*"],
        "Permission": ["aoss:*"]
      },
      {
        "ResourceType": "index",
        "Resource": ["index/*/*"],
        "Permission": ["aoss:*"]
      }
    ],
    "Principal": ["arn:aws:iam::345804339279:user/<YOUR-USERNAME>"],
    "Description": "Self-service full OSS data-plane access"
  }
]
```

6. Click **Create**

### 2.3 Wait for propagation

DAP changes are **asynchronous**. The console shows "saved" instantly but the policy takes **60-90 seconds** to actually apply at the data plane. **Wait at least 90 seconds before retrying your operation.** This is the #1 source of "I did everything but it still doesn't work" reports.

### 2.4 Verify

After waiting, retry whatever was failing. If you were creating an index in OpenSearch Dashboards, refresh the page and try again. If you were running a notebook cell, re-execute it.

You should now see successful index creation, mapping definitions, document inserts, and queries.

---

## What the policy grants

Reading the JSON in plain English:

- **Collection-level rule:** you have `aoss:*` on **every collection** in the account (`collection/*`). This means you can describe, list, and modify collections you own.
- **Index-level rule:** you have `aoss:*` on **every index inside every collection** (`index/*/*`). This means you can create, delete, update, describe indices, and read / write documents in them.
- **Principal:** only YOUR ARN — no one else's permissions are affected by your policy.

The wildcards mean this one DAP covers any collection you ever create — including the one Bedrock KB creates under the hood, any one you create manually for Lab 6.1, and any future ones. Once-and-done.

---

## Common errors and what they mean

| Error you see | What's actually wrong | Fix |
|---|---|---|
| `User: ... is not authorized to perform: aoss:CreateSecurityPolicy` | IAM permissions missing | Confirm the Trainees group is attached to your IAM user (Step 1). |
| `User: ... is not authorized to perform: s3vectors:CreateVectorBucket` | IAM permissions missing for S3 Vectors | Same — confirm Trainees group attached. The group's inline policy includes `s3vectors:*`. |
| `no permissions for [indices:admin/mappings/get]` (status 403) | DAP missing or not applied yet | Create the DAP (Step 2), wait 90s. |
| `Failed to create index "xxx". Access denied when checking existence ...` | Same as above — DAP issue | Create the DAP (Step 2), wait 90s. |
| `User ... not authorized` (DAP exists but doesn't help) | ARN mismatch in Principal | Re-open your DAP in the console → JSON tab → confirm the Principal ARN exactly matches what `aws sts get-caller-identity` returns. ARN is case-sensitive. |
| Errors after creating a brand-new collection | Collection name might not match DAP wildcard | The provided JSON uses `collection/*` and `index/*/*` wildcards — these match anything. If you used a more restrictive Resource pattern, fix that. |

---

## Frequently asked questions

**Q. Do I need to redo the DAP for every collection I create?**
No. The `collection/*` and `index/*/*` wildcards in the JSON cover every collection in your account, current and future. One DAP per user, forever.

**Q. Can multiple trainees share one DAP?**
Yes — the `Principal` field accepts a list of ARNs. But the simpler model is each trainee creates their own (you don't need to coordinate names or wait for someone else).

**Q. Can I just attach `AdministratorAccess` to my IAM user and skip the DAP?**
No. `AdministratorAccess` is an IAM policy — it grants the **first** authorization layer (`aoss:*` actions). The DAP is the **second** layer (data-plane access to indices), and it's a completely separate resource type. Even AWS's `AdministratorAccess` doesn't auto-create DAPs for you.

**Q. Why does AWS make this so complicated?**
OSS data-access policies were designed to let many IAM principals (users, roles, federated identities) share a single OSS collection without needing IAM-level changes for every access pattern. That's good for production multi-tenant workloads but cumbersome for solo trainees. The DAP you're creating is the simplest possible — full access for yourself only.

**Q. I created the DAP but Bedrock KB sync still fails.**
Bedrock KB sync uses the Bedrock SERVICE'S DAP (auto-created by the wizard), not yours. If KB sync fails, it's usually an issue with the wizard's auto-DAP or the IAM execution role for the KB — different problem. Check the KB's "Data sources" tab for the actual sync error.

**Q. The OpenSearch Dashboards URL gives me a different error.**
OSS Dashboards is accessed at a **collection-specific URL** that looks like `https://<collection-id>.<region>.aoss.amazonaws.com/_dashboards/`. If you went to the generic OpenSearch Service "Dashboards" link from the AWS console, you may have hit the wrong endpoint. Find your collection in **OpenSearch Service → Serverless → Collections** → click your collection → use the **OpenSearch Dashboards URL** shown there.

---

## Quick checklist for Lab 6.1 day

Before starting Lab 6.1 (Bedrock Production RAG):

- [ ] Trainees IAM group is attached to your IAM user
- [ ] Your DAP is created with your ARN as Principal (Step 2 above)
- [ ] You waited 90 seconds after creating the DAP
- [ ] Your `.env` file has `AWS_REGION` and AWS credentials set correctly
- [ ] You can run `aws sts get-caller-identity` from the Lab notebook's terminal without errors

If all five are checked, Lab 6.1's first index-creation cell will succeed. If any cell still 403s, see the error table above.

---

*Last updated for Cohort 1, 2026. If something changes on the AWS side and this guide goes stale, ping the instructor on Slack.*
