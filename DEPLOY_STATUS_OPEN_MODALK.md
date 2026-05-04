# Deploying to status-open.modalk.com

## Local workflow

1. Build the production image locally:

```bash
docker build -t ghcr.io/airmantang-svg/model-status:deploy-status-open-modalk .
```

2. Log in to GHCR:

```bash
echo <github_pat> | docker login ghcr.io -u airmantang-svg --password-stdin
```

3. Push the image:

```bash
docker push ghcr.io/airmantang-svg/model-status:deploy-status-open-modalk
```

4. For local verification, copy the production env template and start the already-built image:

```bash
cp .env.production.example .env
docker compose up -d
```

5. Verify health endpoint:

```bash
curl http://127.0.0.1:3100/api/health
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
4. Log in to GHCR on the server if the package is private
5. Pull and start the stack:

```bash
docker compose pull
docker compose up -d
```

6. For later updates:

```bash
git pull
docker compose pull
docker compose up -d
```

## Required production env values

```env
HOST=0.0.0.0
PORT=3100
WEB_ORIGIN=https://status-open.modalk.com
ACCESS_URL=https://status-open.modalk.com
DATABASE_FILE=/app/data/model-status.db
ADMIN_BOOTSTRAP_USERNAME=admin
ADMIN_BOOTSTRAP_PASSWORD=<strong-password>
SESSION_SECRET=<long-random-secret>
```

`open.modalk.com` already uses `127.0.0.1:3000`, so `model-status` should use `127.0.0.1:3100` on this server.

## Reverse proxy

Nginx should proxy:

- `https://status-open.modalk.com`
- to `http://127.0.0.1:3100`

Current production state:

- HTTP requests are redirected to HTTPS
- TLS is issued by Let's Encrypt for `status-open.modalk.com`
- HSTS is enabled: `Strict-Transport-Security: max-age=31536000; includeSubDomains`

## First-time app setup

After deployment:

1. Open `/admin`
2. Log in with the bootstrap admin credentials
3. Add the newapi upstream
4. Sync models
5. Run probes
6. Verify the public dashboard at `/`
