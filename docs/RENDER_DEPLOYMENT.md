# Deploying FINCEPT AI on Render

This project is configured to deploy as one Render Python web service. The FastAPI application serves the built React application and the API under the same HTTPS origin, which keeps its HttpOnly session-cookie authentication intact.

## Before creating the service

1. Push this repository, including `render.yaml`, to GitHub.
2. If `fincept-ai-web` is not available as a Render service name, choose another name. Update **both** the service `name` and the `FRONTEND_ORIGINS` and `TRUSTED_HOSTS` values to use the corresponding `https://<name>.onrender.com` host.
3. Do not commit `.env`, `backend/.env`, a SQLite database, or OpenAI keys.

## Deploy

1. In Render, choose **New +** then **Blueprint** and connect the GitHub repository.
2. Render finds `render.yaml`. Create the `fincept-ai-web` service.
3. Keep the persistent disk enabled. The application stores its SQLite database at `/var/data/fincept.sqlite3`; without the disk, users and financial records disappear when the service restarts or redeploys.
4. After the service becomes live, open its `/api/health` URL. It should return `{"status":"ok"}`. Then visit the root URL and register an account.

## Optional AI assistant

Add these only in the Render service's Environment page, never to source control:

- `AI_PROVIDER=openai`
- `AI_MODEL=<your enabled compatible model ID>`
- `OPENAI_API_KEY=<secret key>`

Redeploy after saving them. Without them, the rest of the app works but the assistant reports that a provider is unavailable.

## Notes

- The configured disk requires a paid Render web-service plan. Render's free plan has an ephemeral filesystem and cannot safely host this SQLite-backed application.
- For a production system that needs horizontal scaling or stronger database guarantees, migrate from SQLite to Render Postgres before adding replicas.
