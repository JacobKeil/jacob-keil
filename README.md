# Jacob Keil Portfolio

A personal portfolio built with SvelteKit, Svelte, TypeScript, and Tailwind CSS. It follows the Docker, Nginx, and Certbot production setup used by Clover Trivia and does not need a database.

## Local development

Install [Bun](https://bun.sh/) and run:

```sh
bun install
cp .env.example .env
bun run dev
```

Open the local URL printed by Vite. Edit `src/routes/+page.svelte` to update the introduction, projects, biography, and contact links. Styling lives in `src/app.css`.

Useful commands:

```sh
bun run check
bun run build
bun run preview
```

## Production with Docker Compose

The production stack runs the SvelteKit app, Nginx, and Certbot. Nginx handles HTTPS, and Certbot renews the certificate. There is no database container or database configuration.

On the server:

1. Install Docker Engine and Docker Compose.
2. Clone this repository to a persistent directory.
3. Point the domain's DNS A/AAAA records at the server and allow inbound ports 80 and 443.
4. Build and publish the first app image to your Docker registry, then set `APP_IMAGE` in the production environment file. The image must be available before the bootstrap script starts the app:

```sh
cp .env.production.example .env.production
chmod 600 .env.production
# Edit DOMAIN, CERTBOT_EMAIL, ORIGIN, and APP_IMAGE.
# Build and push the image named in APP_IMAGE from a machine with Docker access.
docker build -t your-dockerhub-user/jacob-keil-portfolio:latest .
docker push your-dockerhub-user/jacob-keil-portfolio:latest
./scripts/init-letsencrypt.sh
```

The bootstrap script starts the app and HTTP Nginx config, requests the certificate, creates the TLS Nginx config, and enables automatic renewal. Run it from the repository directory after DNS is active.

Build and publish the image to your Docker registry, then deploy it on the server:

```sh
docker compose --env-file .env.production -f docker-compose.production.yml pull app
docker compose --env-file .env.production -f docker-compose.production.yml up -d --remove-orphans
```

Check service status and logs with:

```sh
docker compose --env-file .env.production -f docker-compose.production.yml ps
docker compose --env-file .env.production -f docker-compose.production.yml logs -f app
```

## GitHub Actions deployment

`.github/workflows/deploy.yml` checks and builds the app, publishes Docker images, then deploys the commit image over SSH when code is pushed to `main`. Configure these GitHub repository secrets:

| Secret | Purpose |
| --- | --- |
| `DOCKERHUB_USERNAME` | Docker Hub user or organization |
| `DOCKERHUB_TOKEN` | Docker Hub publishing token |
| `SERVER_HOST` | Server IP or hostname |
| `SERVER_USER` | SSH deployment user |
| `SERVER_SSH_KEY` | Private SSH key for that user |
| `DEPLOY_PATH` | Absolute path to the server checkout |

The server checkout must be on `main`, have an `origin` remote, and be able to run `git pull --ff-only` without prompting. Configure the repository secrets and server before enabling this workflow on a live repo.
