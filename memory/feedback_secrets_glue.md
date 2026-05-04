---
name: All secrets via Secrets Manager in Glue jobs
description: In Glue jobs, ALL connection strings must come from AWS Secrets Manager — never as plain job arguments
type: feedback
---

Never pass connection strings (MongoDB URI, Azure Queue connstr, API tokens) as plain Glue job arguments. Always fetch from Secrets Manager at runtime.

**Why:** User requirement — connection strings must not appear as Glue job arguments anywhere (console, code, JSON config).

**How to apply:** Glue job args carry only the SECRET IDs (e.g. `--MONGODB_SECRET "company-name/mongodb"`). Runtime code calls `get_secret(secret_id, key)` via boto3 to get the actual value. Same pattern as the skill_mapper lambda (`utils.py:get_secret`).
