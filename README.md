# Phoenix + LiveView + Keycloak + OpenLDAP Template

This repository is a **template** for a Phoenix + LiveView application that uses:

- **PostgreSQL** (the default database used by Phoenix)
- **Keycloak** as the identity provider
- **Osixia OpenLDAP** as the user store behind Keycloak

The template provides Docker Compose services for PostgreSQL, Keycloak, and OpenLDAP,
plus a starter Keycloak realm that connects to the LDAP server.

## What you get

- `docker-compose.yml` with:
  - `phoenix-db` (Postgres) for your Phoenix app
  - `keycloak` (Keycloak) for authentication
  - `keycloak-db` (Postgres) for Keycloak
  - `openldap` (Osixia OpenLDAP)
- `keycloak/realm-export.json` realm import with LDAP federation
- `openldap/ldif/50-bootstrap.ldif` with a sample user
- `.env.example` with development-friendly (non-default) credentials

## Getting started

### 1) Prepare environment variables

```bash
cp .env.example .env
```

> You can tweak any value in `.env` for your local development setup.

### 2) Start the identity stack

```bash
docker compose up -d
```

This launches PostgreSQL, OpenLDAP, Keycloak, and Keycloak's database.

### 3) Log into Keycloak

- URL: http://localhost:8080
- Admin user: `admin`
- Admin password: `KC_ADMIN_PASSWORD` from your `.env`

The realm `phoenix` is imported automatically.

### 4) Generate a Phoenix application

Phoenix is not generated for you in this template because you may choose a
project name, app structure, or flags. Create a new app in this repo with:

```bash
mix phx.new my_app --database postgres
```

Then configure the database settings to match `.env`:

- Host: `localhost`
- Port: `5432`
- Username: `PHX_DB_USER` from `.env`
- Password: `PHX_DB_PASSWORD` from `.env`
- Database: `PHX_DB_NAME` from `.env`

### 5) Wire up authentication

The provided Keycloak realm uses LDAP federation. You can use `Ueberauth`,
`PowAssent`, or `Phoenix` OIDC libraries to integrate your Phoenix app with
Keycloak.

## LDAP & Keycloak details

- LDAP base DN: `dc=phoenix,dc=test`
- Sample user: `uid=devuser,ou=people,dc=phoenix,dc=test`
- Sample user password: `LDAP_USER_PASSWORD` from `.env` (also stored in `openldap/ldif/50-bootstrap.ldif`)
- Keycloak realm: `phoenix`

Keycloak is configured to connect to LDAP using the **readonly** LDAP user
that OpenLDAP creates automatically.

## Notes

- This template avoids default credentials but keeps development-friendly
  passwords in `.env.example`.
- For production use, replace all secrets and consider hardening LDAP and
  Keycloak configuration.
