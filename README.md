# WinAppLicenseConfig

Dynamic API endpoint configuration for **AccountrixFlow** license server.

The WinForm client reads this file on startup to discover the current license server URL. This avoids hardcoding the API domain, which changes when free hosting expires.

## How It Works

1. This repo contains `api-endpoint.json` with the current API base URL
2. The WinForm client fetches this file via the GitHub raw URL on every startup
3. The resolved URL is cached locally, so the app works even if GitHub is briefly unreachable
4. When the hosting domain changes, update this file and push — all clients auto-discover

## When Hosting Changes

1. Create the new hosting account (e.g., on MonsterASP.net)
2. Deploy `WinAppLicenseServer` to the new host
3. Edit `api-endpoint.json` below with the new URL
4. Commit and push to GitHub
5. All clients will pick up the new URL on their next startup

## File Format

```json
{
  "apiBaseUrl": "https://your-new-subdomain.runasp.net",
  "updatedAt": "2026-02-07"
}
```

- **apiBaseUrl** — The full base URL of the license server (no trailing slash)
- **updatedAt** — Date of last update (for your reference only)
