# Nginx Docker SSL sample

A small practice stack: a static page behind Nginx, with HTTP redirected to HTTPS. This is my own sample, not client work.

## Local

Docker Desktop must be running.

```powershell
copy .env.example .env
docker compose up
```

Open https://localhost. The first start writes a self-signed certificate into `certs/`. The browser warning is expected. Port 80 only redirects to HTTPS, except `/.well-known/acme-challenge/`.

Stop with Ctrl+C, then `docker compose down`.

## Production note

Do not point this sample at a real server from the README. When you have a VPS and a domain:

1. Set an A record for the domain to the server IP.
2. Copy this folder to the server and set `DOMAIN` in `.env` to that hostname.
3. Replace the self-signed files. Certbot writes the real pair. Mount those files where Nginx already reads them:

```text
/etc/letsencrypt/live/$DOMAIN/fullchain.pem -> /etc/nginx/certs/fullchain.pem
/etc/letsencrypt/live/$DOMAIN/privkey.pem   -> /etc/nginx/certs/privkey.pem
```

4. The HTTP server already serves `/.well-known/acme-challenge/` from `/var/www/certbot`, so an HTTP-01 challenge can complete before the rest of port 80 redirects to HTTPS.
5. Reload Nginx after the new files are in place: `docker compose exec proxy nginx -s reload`.

No secrets belong in Git. `certs/*.pem` and `.env` are ignored.
