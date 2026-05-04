---
description: Fetch an Azure DevOps wiki page by URL (no path-based trial and error).
---

Retrieve the wiki page at `$ARGUMENTS` and print its content.

**Use this exact recipe — do NOT try path-based lookups first, they fail on titles with special characters (arrows, spaces, accents).**

1. Parse the URL. Azure DevOps wiki URLs look like:
   `https://dev.azure.com/<org>/<project>/_wiki/wikis/<wiki>/<id>/<slug>`
   Extract: `org`, `project`, `wiki` (e.g. `company-name.wiki`), `id` (the integer right after `/wikis/<wiki>/`).

2. Fetch by id (the only reliable method):
   ```
   az devops invoke \
     --area wiki --resource pages \
     --organization https://dev.azure.com/<org> \
     --route-parameters project=<project> wikiIdentifier=<wiki> \
     --query-parameters id=<id> includeContent=true \
     --api-version 7.0
   ```

3. The response is JSON; the page body is in the `content` field. Print that content as-is to the user (preserve markdown).

**Fallback** — if `<id>` is missing from the URL (UUID-form `?pagePath=...`), then and only then use:
```
az devops wiki page show --organization https://dev.azure.com/<org> --project <project> --wiki <wiki> --path "<decoded-pagePath>" --include-content
```
URL-decode `pagePath` first (`%2F` → `/`, `%20` → space, `%E2%86%92` → →).

**Do not** use `WebFetch` — wiki pages require auth.
