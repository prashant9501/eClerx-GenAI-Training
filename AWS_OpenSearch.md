# AWS OpenSearch — Trainee Setup Guide

The eClerx GenAI training touches **two different AWS OpenSearch services** that are easy to confuse, have different consoles, and have completely different authorization models. This guide covers the one-time setup for both.

**Read this BEFORE starting Module 4 Lab 4.4, Module 6 Lab 6.1, or the Capstone.** A few minutes of setup here saves an hour of debugging 403 errors later.

---

## The two services (don't mix them up)

| | **OpenSearch Service** (classic) | **OpenSearch Serverless** (OSS) |
|---|---|---|
| What it is | EC2-backed managed cluster you provision | Fully managed, pay-per-OCU, zero infrastructure |
| Console terminology | **Domains** | **Collections** |
| AWS Console URL slug | `/aos/...` | `/aoss/...` |
| Endpoint format | `xxx.us-east-1.es.amazonaws.com` | `xxx.us-east-1.aoss.amazonaws.com` |
| IAM service code | `es` (e.g., `es:ESHttpPut`) | `aoss` (e.g., `aoss:CreateCollection`) |
| **Auth model** | IAM + **Fine-Grained Access Control (FGAC)** with role mapping inside the cluster | IAM + **Data Access Policy (DAP)** as a separate AWS resource |
| Used by | **Lab 6.1** (you instantiate it directly via `opensearch-py`) | **Lab 4.4 + Capstone** (created automatically by Bedrock Knowledge Base wizard) |

**Key takeaway:** the setup steps for the two services are completely different. Doing the OSS setup will NOT help Lab 6.1, and vice-versa. You need to do both.

---

## Per-lab setup checklist

| Lab | Service | What you need | Section below |
|---|---|---|---|
| **Lab 4.4 — Bedrock Knowledge Bases** | Serverless (managed by Bedrock KB) | Bedrock KB created via console wizard + a Serverless DAP for inspection | §B |
| **Lab 6.1 — Bedrock Production RAG** | Classic Service domain | Your own Service domain (already created) + FGAC role mapping | §A |
| **Capstone — Email Triage Agent** | Serverless (via Bedrock KB) | Same as Lab 4.4 | §B |
| All other labs | None | — | — |

If you're only doing Lab 6.1 today, jump to **§A**.
If you're only doing Lab 4.4 / Capstone today, jump to **§B**.
For Day 6 + Day 4 + Capstone, do both.

---

## §A — Setup for Lab 6.1 (classic OpenSearch Service Domain + FGAC)

Lab 6.1 uses your existing OpenSearch Service domain (e.g., `prashant-opensearch`) directly via `opensearch-py`. The lab will fail on the first index-creation cell with a **403 `security_exception`** error like:

> `no permissions for [indices:admin/mappings/get]`
> `Failed to create index "xxx". Access denied when checking existence of index xxx via HTTP HEAD request.`

This is FGAC saying "I don't recognise your IAM identity as having any internal OpenSearch role." Even with `AdministratorAccess` on your IAM user, you must explicitly map your IAM ARN to an OpenSearch role inside the cluster.

### A.1 Find your IAM user ARN

Either:
- AWS Console → **IAM → Users → your username** → copy the **User ARN** at the top, OR
- Run `aws sts get-caller-identity` and copy the `Arn` field

It looks like: `arn:aws:iam::345804339279:user/<YOUR-USERNAME>`

### A.2 Open OpenSearch Dashboards for YOUR domain

Important: do NOT use the AWS Console "Create index" page (that's where you got the 403). Go to OpenSearch Dashboards instead:

1. AWS Console → **Amazon OpenSearch Service** → **Domains** → click your domain (e.g., `prashant-opensearch`)
2. On the domain detail page, find the **OpenSearch Dashboards URL** (under "General information")
3. Click it — opens a new tab with the Dashboards login screen

### A.3 Log in to Dashboards

Use the **master user credentials** you set when creating the domain. This is either:
- An internal master username + password (most common — you set these in the domain creation wizard), OR
- An IAM-ARN master user (if you set this, your AWS console SSO should auto-authenticate; if not, see the FAQ)

If you can't remember your master password, you can reset it from the AWS Console: **Domain → Security configuration → Edit → Set new master user password**. (Costs ~5 min for the domain to apply the change.)

### A.4 Map your IAM user to `all_access` role

Once logged into Dashboards:

1. Click the **hamburger menu** (top left, three horizontal lines)
2. Scroll down to **Security** (under Management section)
3. Click **Roles**
4. Click `all_access` (it's near the top — opens the role detail page)
5. Click the tab **Mapped users**
6. Click button **Manage mapping**
7. Under **Backend roles** (NOT "Users" — backend roles is the section that takes ARNs), click **Add another**
8. Paste your IAM user ARN from §A.1:
   ```
   arn:aws:iam::345804339279:user/<YOUR-USERNAME>
   ```
9. Click **Map**

### A.5 Wait for propagation, then verify

Wait ~30 seconds for the role mapping to take effect. Then go back to your AWS Console "Create index" page and retry — it should now succeed. Or open a new Lab 6.1 notebook cell and run:

```python
opensearch_client.info()
# Should return cluster info, not 403
```

### A.6 You're done with §A

Lab 6.1's index creation, document insertion, and similarity search will all work now. Move on to running the lab.

---

## §B — Setup for Lab 4.4 + Capstone (Bedrock KB + Serverless DAP)

Lab 4.4 and the Capstone use Amazon Bedrock Knowledge Bases (BKB), which is a managed service. Underneath, BKB creates an **OpenSearch Serverless** collection automatically. You don't need to set up OSS yourself — the BKB wizard does it for you.

**However**, you'll hit two kinds of permission issues:

1. **IAM permissions** to let the Bedrock console wizard create the OSS resources (collection + security policies + access policies)
2. A **Data Access Policy (DAP)** if you want to inspect the OSS collection or its indices directly (not strictly required to use BKB; required if you want to debug it)

### B.1 IAM permissions (already done for you)

Your **Trainees** IAM group has the right managed and inline policies attached. You don't need to do anything here — just confirm via AWS Console → IAM → Users → your username → Permissions tab → you should see the Trainees group.

### B.2 Create the Bedrock Knowledge Base

Follow Lab 4.4's instructions OR the Capstone brief §8. The console wizard will:
1. Create an OSS collection (`bedrock-knowledge-base-XXXXXX`)
2. Create a security policy + encryption policy
3. Create a default Data Access Policy that grants the Bedrock SERVICE access to the collection
4. Create the KB itself + a default index

Save the KB ID — you'll need it for `INDUS_KB_ID` in `.env`.

### B.3 Create your own DAP for index inspection (optional but recommended)

The DAP the wizard creates only grants Bedrock service access — not your IAM user. If you try to inspect the index directly (via OpenSearch Dashboards or `opensearch-py` against the OSS collection), you'll get a 403 like:

> `no permissions for [indices:admin/mappings/get]`

Self-service fix:

1. AWS Console → **Amazon OpenSearch Service** → in left nav, under **Serverless**, click **Data access policies**
2. Click **Create access policy** (top right)
3. Settings:
   - **Name:** `<your-username>-fullaccess` (e.g., `prashant-fullaccess` — must be unique within the AWS account)
   - **Type:** Data access
   - Click **JSON** tab
4. Paste this — change `<YOUR-USERNAME>` to your IAM username:

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

5. Click **Create**
6. Wait **60-90 seconds** for propagation (OSS DAP changes are async; #1 source of "still doesn't work" reports)
7. You can now inspect the OSS collection via OpenSearch Dashboards or direct API calls

### B.4 You're done with §B

Lab 4.4 and the Capstone's `bedrock_agent_runtime.retrieve` and `retrieve_and_generate` calls will work. The DAP only matters if you want to inspect the underlying index for debugging.

---

## Common 403 errors and what they mean

| Error you see | Service | What's actually wrong | Fix |
|---|---|---|---|
| `User: ... is not authorized to perform: aoss:CreateSecurityPolicy` | Serverless (Lab 4.4 / Capstone) | IAM permissions missing for OSS | Confirm Trainees group is attached |
| `User: ... is not authorized to perform: s3vectors:CreateVectorBucket` | Serverless (Lab 4.4 / Capstone) | IAM permissions missing for S3 Vectors | Same — confirm Trainees group attached |
| `no permissions for [indices:admin/mappings/get]` (status 403, type `security_exception`) — when working with a Bedrock-KB collection | Serverless (DAP issue) | DAP missing or not propagated | §B.3 (DAP creation), wait 90s |
| `no permissions for [indices:admin/mappings/get]` — when working with your own classic Domain | **Classic Service** (FGAC issue) | Your IAM ARN isn't mapped to any OpenSearch role inside the cluster | §A (FGAC role mapping), wait 30s |
| `Failed to create index "xxx". Access denied when checking existence of index xxx via HTTP HEAD request.` (in AWS Console) | **Classic Service** | Same as above — FGAC | §A |
| Errors after creating the role mapping but before waiting | Either | Propagation lag | Wait — Service ~30s, Serverless ~60-90s |
| Errors after the wait, role/DAP exists | Either | ARN mismatch (case-sensitive, no whitespace) | Re-open mapping/DAP, confirm ARN exactly matches `aws sts get-caller-identity` |

---

## Frequently asked questions

**Q. I'm running Lab 6.1 and Lab 4.4 — do I really need to set up BOTH services?**
Yes. They're different AWS resources used for different purposes. Lab 6.1 uses your classic Service domain directly; Lab 4.4 uses a Serverless collection wrapped in Bedrock KB. The auth model is different for each.

**Q. Can I just delete my classic Domain and use Serverless for everything?**
Lab 6.1's notebook is hardcoded to use the classic Service auth pattern (`AWS4Auth` with service code `'es'` and a domain endpoint). To use Serverless instead, the notebook would need surgery (different auth, different endpoint format, different index creation API). Don't try it as a trainee — stick with the classic Domain for Lab 6.1.

**Q. My Domain was created with IAM ARN as master user, not internal username/password. Do I still need to map?**
If your IAM user IS the master user, you should already have full access. But map yourself in the `all_access` role anyway as a safety net — costs nothing, and the AWS Console "Create index" UI sometimes uses code paths that benefit from explicit role mapping even for the master.

**Q. I forgot my master password.**
AWS Console → your domain → **Security configuration → Edit → Set new master user password**. Wait ~5 min for the domain to apply. Then log in to Dashboards with the new password.

**Q. The OpenSearch Dashboards URL gives me a different error or just a blank page.**
Make sure you're using the URL from the Domain's "General information" panel, not an old bookmark. The URL format is `https://<dashboard-id>.<region>.es.amazonaws.com/_dashboards/`. If still failing, try Incognito mode (rules out cached SSO sessions).

**Q. Can the instructor pre-create one DAP for all trainees instead of each trainee creating their own?**
Technically yes (the `Principal` array accepts multiple ARNs). But each trainee creating their own scales better — no coordination needed, no waiting on instructor.

**Q. Does the FGAC role mapping persist if I delete and recreate my domain?**
No. Role mappings live inside the cluster's security index. If you delete the domain, the mappings are gone. Same for DAPs on Serverless — DAPs are per-account and survive collection deletion, but cleanup eventually.

**Q. Is my classic Domain expensive to keep idle?**
Depends on the instance type. `t3.small.search` is ~$24/mo idle (cheapest dev option). `m6g.large.search` is ~$98/mo. For the take-home + Day 6, total cost should be under $20 if you delete the domain after the program. Check your domain config under "Cluster configuration".

---

## Pre-lab checklists

### Before Lab 6.1 (classic Service / FGAC)

- [ ] Trainees IAM group attached to your IAM user
- [ ] You have a Service Domain created (e.g., `prashant-opensearch`)
- [ ] You can find your domain's "OpenSearch Dashboards URL"
- [ ] You've logged into Dashboards successfully (using master user creds)
- [ ] Your IAM user ARN is mapped as a backend role in the `all_access` role (§A.4)
- [ ] You waited ~30 seconds after the mapping
- [ ] Your `.env` has `OPENSEARCH_ENDPOINT=<your-domain>.us-east-1.es.amazonaws.com`

If all 7 are checked, Lab 6.1's first index-creation cell will succeed.

### Before Lab 4.4 + Capstone (Serverless via Bedrock KB)

- [ ] Trainees IAM group attached to your IAM user
- [ ] You've created a Bedrock Knowledge Base via the console wizard
- [ ] The KB's data source has been synced (status = "Available" in the Data sources tab)
- [ ] (Optional, for debugging) You've created a Serverless DAP with your ARN as Principal (§B.3)
- [ ] Your `.env` has `INDUS_KB_ID=<your-kb-id>` (Capstone) or the equivalent for Lab 4.4

If all are checked, `bedrock_agent_runtime.retrieve` will return chunks from your KB.

---

*Last updated for Cohort 1, 2026. If something changes on the AWS side and this guide goes stale, ping the instructor on Slack.*
