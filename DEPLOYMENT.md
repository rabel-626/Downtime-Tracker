# GitHub Pages Deployment — Downtime Tracker

## Recommended repository

`abel-downtime-tracker`

Default GitHub Pages address:

`https://rabel-626.github.io/abel-downtime-tracker/`

Custom address:

`https://tracker.abelengineering.com`

## Publish

1. Create a public GitHub repository named `abel-downtime-tracker` under `rabel-626`.
2. Upload every file from this package to the repository root.
3. Open **Settings → Pages**.
4. Under **Build and deployment**, select **Deploy from a branch**.
5. Select the `main` branch and `/ (root)`, then save.
6. Under **Custom domain**, enter `tracker.abelengineering.com` and save.
7. At the DNS provider for `abelengineering.com`, create the record below.

| Type | Name | Target |
| --- | --- | --- |
| CNAME | `tracker` | `rabel-626.github.io` |

8. After GitHub confirms the DNS check, enable **Enforce HTTPS**.

Do not add `https://`, the repository name, or a trailing slash to the DNS target.
