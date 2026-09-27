# Universal Video Player & Downloader

A commercial-style React + TypeScript video player with a small Node/Express inspection API.

## Supported

- Direct MP4
- Direct WebM
- Direct OGG/OGV
- Public HLS/M3U8 where the browser can play it
- Recent URL history
- Clipboard paste/copy
- Drag & drop
- Picture-in-picture where supported
- Fullscreen
- Playback speed
- HLS quality selection
- Mobile-first responsive UI
- Express security headers, CORS and rate limiting

## Important source limitation

This project deliberately does **not** bypass DRM, authentication, paywalls, anti-download controls, private files, expiring protected URLs, or provider security mechanisms.

Terabox/Diskwala adapters are included as safe placeholders. They return the requested unsupported-source message until an official/public provider API or authorized playback endpoint is configured.

The backend is an inspector, not an open proxy. It does not fetch arbitrary remote media on behalf of the browser.

## Requirements

Node.js 20+ recommended.

## Install

From the project root:

```bash
npm install
npm install --workspace backend dotenv
```

Copy the environment example:

```bash
cp backend/.env.example backend/.env
```

On Windows PowerShell:

```powershell
Copy-Item backend/.env.example backend/.env
```

## Development

```bash
npm run dev
```

Frontend:
http://localhost:5173

Backend:
http://localhost:8787

## Production build

```bash
npm run build
npm start
```

## Notes about downloads

For a direct media URL, the browser receives the URL through the normal HTML download mechanism. Some remote servers may block cross-origin downloads or force an inline response. That is a server/browser restriction; this app intentionally does not proxy arbitrary media to defeat it.

HLS is playback-only in this implementation. Segment downloading/repackaging is intentionally not implemented.

## Adding a legitimate provider

Create a new adapter in:

```text
backend/src/adapters/
```

Implement:

```ts
{
  name,
  matches(url),
  inspect(url)
}
```

Only return a `mediaUrl` when it comes from a documented/public/authorized playback mechanism. Do not scrape private pages or bypass controls.

Then register the adapter in:

```text
backend/src/adapters/index.ts
```

## Deployment

### Frontend

Build:

```bash
npm run build --workspace frontend
```

Deploy `frontend/dist` to a static host such as a standard static-site service.

Set the frontend API base/proxy to your deployed backend if frontend and backend use different origins. For a simple same-origin deployment, serve the frontend from your Node application or configure the reverse proxy.

### Backend

Build:

```bash
npm run build --workspace backend
```

Start:

```bash
npm start
```

Set:

```env
PORT=8787
CORS_ORIGIN=https://your-frontend.example
MAX_REQUESTS_PER_MINUTE=60
```

Use HTTPS in production.

## Keyboard shortcuts

- Enter: inspect/play while the URL field is focused
- Ctrl/Cmd + L: focus URL field
- Escape: clear URL and player state

## Security

- Helmet security headers
- CORS allowlist
- Request rate limiting
- Zod input validation
- HTTP(S)-only URLs
- No embedded credentials
- No arbitrary proxy endpoint
- No private API keys in frontend
