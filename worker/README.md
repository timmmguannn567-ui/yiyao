# RA Mail Studio API

This Cloudflare Worker keeps the Alibaba Cloud Model Studio (DashScope) API key off the browser and exposes one authenticated endpoint to the GitHub Pages frontend.

## Required secrets

From this directory, authenticate Wrangler and create two encrypted secrets:

```powershell
npx wrangler login
npx wrangler secret put DASHSCOPE_API_KEY
npx wrangler secret put APP_ACCESS_TOKEN
npx wrangler deploy
```

- `DASHSCOPE_API_KEY`: an Alibaba Cloud Model Studio API key. Never commit it.
- `APP_ACCESS_TOKEN`: a long random access code shared only with approved users of this internal tool.

The default endpoint is the China (Beijing) OpenAI-compatible DashScope endpoint and the default model is `qwen3.8-max`. For production, set `DASHSCOPE_BASE_URL` to your workspace-specific compatible-mode URL and ensure the API key belongs to the same region and workspace.

After deployment, copy the Worker URL and set `apiEndpoint` in the repository-root `config.js`, for example:

```js
window.RA_MAIL_CONFIG = Object.freeze({
  apiEndpoint: "https://ra-mail-studio-api.example.workers.dev/v1/regulatory-email",
  modelLabel: "通义千问 Qwen3.8-Max",
  requestTimeoutMs: 60000
});
```

The access code is entered by the user at runtime and retained only in `sessionStorage`. The frontend falls back to its local rules if the service is unavailable.

## Security notes

- The Worker checks the browser origin, bearer access code, request size, enumerated settings, and input length.
- No email text is deliberately written to application logs. Confirm the data-processing region and your organization's information-security requirements before using real regulated or confidential content.
- The in-memory rate limiter is a lightweight abuse guard, not a global quota. For a wider rollout, add Cloudflare Rate Limiting or authenticated company SSO.
- Users must remove personal data and unauthorized confidential information before submission and must perform human regulatory, medical, quality, and legal review.
