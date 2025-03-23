# Nginx

Generate `dhparam.pem`, `self-signed.crt`, and `self-signed.key` and place them in [/nginx/ssl-self]() so that nginx can
accept SSL connections.

# Database

## Using an existing database

Copy/move the database data directory to [/postgres-data]() and run `docker compose up`.

## Setting up a new database

### PostgreSQL

#### Prerequisites for first run

Define `POSTGRES_PASSWORD` in [docker-compose.yml]() e.g. with:

```yaml
services:
  database:
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/postgres-passwd
    secrets:
      - postgres-passwd
secrets:
  postgres-passwd:
    file: ./postgres-passwd.secret
```

If using a password file like this example make sure that the file in question exists and has content.

You should then remove the mount and update the stack.

#### Restore database content

Mount a database dump e.g. with:

```yaml
services:
  database:
    volumes:
      - ./austmus-pg_dumpall:/mnt/db/austmus-pg_dumpall
```

Then restore by executing psql through docker, e.g.:

```shell
docker compose exec devt-ala-stack-database-1 psql -X -f /mnt/db/austmus-pg_dumpall postgres -U postgres
```

After the restore you should remove the mount.