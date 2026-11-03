# MCPDBWizard for Postgres

MCPDBWizard for Postgres generates MCP servers from a PostgreSQL schema. You select the tables,
views, functions, procedures and SQL statements to expose, and it generates a server for exactly
those. An agent then reaches the database only through typed tools. Nothing you didn't select has
any code generated for it, and every call binds values rather than passing SQL text.

It ships as a Docker image. The web console runs inside it: **Design** chooses what to expose,
and **Runtime** generates, starts and fronts the servers over HTTP.

This repository holds no source code. It exists for the
[release notes](https://github.com/srmadscience/mcpdbwizard-postgres/releases) and as a public home
for the image.

## Get the image

```
docker pull ghcr.io/srmadscience/mcpdbwizard-postgres:latest
docker pull docker.io/srmadscience/mcpdbwizard-postgres:latest
```

Both registries hold the same multi-arch image (`linux/amd64`, `linux/arm64`). **Pin a version
tag in anything that matters.** `latest` moves, and `docker run` never refreshes an image the
machine already has.

## Run it

```
docker run -d --name mcpdbwizard-postgres -p 8080:8080 -v mcpdbwizard-data:/data \
  -e MCPDBWIZARD_DB_HOST=db.example.com \
  -e MCPDBWIZARD_DB_NAME=appdb \
  -e MCPDBWIZARD_DB_USER=appuser \
  -e DB_PASS_FILE=/run/secrets/db \
  -e ADMIN_INITIAL_PASSWORD='choose-a-long-one' \
  ghcr.io/srmadscience/mcpdbwizard-postgres:latest
```

Open <http://localhost:8080>, log in as `admin` with `ADMIN_INITIAL_PASSWORD`, and set a new
password when asked.

| Setting | Meaning |
| --- | --- |
| `MCPDBWIZARD_DB_HOST` | The database host |
| `MCPDBWIZARD_DB_PORT` | Its port; 5432 if unset |
| `MCPDBWIZARD_DB_NAME` | The database |
| `MCPDBWIZARD_DB_USER` | The login role, and the first schema listed |
| `MCPDBWIZARD_DB_OTHER_USER` | Optional: more schemas to list, as a `LIKE` pattern (`app%`) |
| `DB_PASS_FILE` or `DB_PASS` | The role's password: a file (preferred) or the value; not both |
| `ADMIN_INITIAL_PASSWORD` | The console's first `admin` password |

`/data` holds the configs and the generated servers. Keep it on a volume.

## Supported versions

PostgreSQL 16, 17 and 18. PostGIS geometry and geography cross as EWKT, and pgvector types as
number arrays.

## Documentation and licence

Documentation is at <https://mcpdbwizard.com/docs/>. The image is proprietary software from ATB
Consultancy Services Ltd, and an MCPDBWizard licence covers both the Oracle and the Postgres
product. See <https://mcpdbwizard.com> for terms.

## Reporting a problem

To report a suspected **security** problem, use this repository's **Security** tab →
**Report a vulnerability**, which keeps the report private. Please don't open a public issue for
it. For anything else, open an issue. Include the image tag, the PostgreSQL version
(`select version()`), and a minimal config or steps that reproduce it.
