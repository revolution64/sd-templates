# sd-templates

Reusable GitHub Actions workflows, triggered from other repos.

## `deploy-express-app.yml`

Deploys a Dockerised Express app to a Hetzner box, behind nginx with a
Let's Encrypt certificate.

```yaml
jobs:
  deploy:
    uses: revolution64/sd-templates/.github/workflows/deploy-express-app.yml@main
    with:
      app_name: my-app
      domain: my-app.example.com
      port: "3008"
      environment: dev
    secrets:
      HETZNER_HOST: ${{ secrets.HETZNER_HOST }}
      HETZNER_USERNAME: ${{ secrets.HETZNER_USERNAME }}
      HETZNER_SSH_KEY: ${{ secrets.HETZNER_SSH_KEY }}
      CERTBOT_EMAIL: ${{ secrets.CERTBOT_EMAIL }}
      APP_ENV_VARS: ${{ secrets.APP_ENV_VARS }}
```

### Inputs

| Input         | Required | Default | Description                                                            |
| ------------- | -------- | ------- | ---------------------------------------------------------------------- |
| `app_name`    | yes      |         | Unique name (container, nginx config, `/opt/<app_name>` directory)      |
| `domain`      | yes      |         | Domain to serve the app on                                             |
| `port`        | yes      |         | Port the app listens on inside Docker                                  |
| `host_port`   | no       | `port`  | Port exposed on the host                                               |
| `app_path`    | no       | `''`    | Subdirectory of the repo containing the app (monorepo support)          |
| `branch`      | no       | ref     | Branch to check out                                                    |
| `skip_ssl`    | no       | `false` | Skip certbot / SSL setup                                               |
| `environment` | no       | `dev`   | GitHub environment to read secrets from                                |

### Monorepos

The whole repository is copied to `/opt/<app_name>`. When `app_path` is set,
the `.env` file is written to and `docker compose` is run from
`/opt/<app_name>/<app_path>`, so each app in a monorepo can have its own
`Dockerfile` and `docker-compose.yml`:

```yaml
with:
  app_name: my-app
  app_path: apps/my-app
  domain: my-app.example.com
  port: "3040"
```
