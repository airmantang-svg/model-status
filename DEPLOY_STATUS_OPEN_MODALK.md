# Deploying to status.open.modalk.com

## Local workflow

1. Copy production env template:

```bash
cp .env.production.example .env
```

2. Adjust values in `.env` for the target environment.

3. Build and run locally with Docker Compose:

```bash
docker compose up -d --build
```

4. Verify health endpoint:

```bash
curl http://127.0.0.1:3000/api/health
```

## Server layout

Recommended server directory:

```text
/opt/model-status/
  app/
  data/
```

Suggested deployment flow on the server:

1. Clone this fork into `/opt/model-status/app`
2. Copy `.env.production.example` to `/opt/model-status/app/.env`
3. Update production secrets and domain values
4. Start the stack:

```bash
docker compose up -d --build
```

## Required production env values

```env
HOST=0.0.0.0
PORT=3000
WEB_ORIGIN=https://status.open.modalk.com
ACCESS_URL=https://status.open.modalk.com
DATABASE_FILE=/app/data/model-status.db
ADMIN_BOOTSTRAP_USERNAME=admin
ADMIN_BOOTSTRAP_PASSWORD=<strong-password>
SESSION_SECRET=<long-random-secret>
```

## Reverse proxy

Nginx should proxy:

- `https://status.open.modalk.com`
- to `http://127.0.0.1:3000`

## First-time app setup

After deployment:

1. Open `/admin`
2. Log in with the bootstrap admin credentials
3. Add the newapi upstream
4. Sync models
5. Run probes
6. Verify the public dashboard at `/`
