# PostgreSQL with InsForge extensions and init scripts
# Pre-built image for cloud deployments (Railway, Render, Fly.io, etc.)
#
# Build: docker build -t ghcr.io/insforge/postgres-all:latest -f railway/postgres/Dockerfile .
# Push:  docker push ghcr.io/insforge/postgres-all:latest

FROM ghcr.io/insforge/postgres:v15.13.2

# Copy initialization scripts into the image
# These run automatically on first start when data directory is empty
COPY docker-init/db/db-init.sql /docker-entrypoint-initdb.d/01-init.sql
COPY docker-init/db/jwt.sql /docker-entrypoint-initdb.d/02-jwt.sql
COPY docker-init/db/logs.sql /docker-entrypoint-initdb.d/03-logs.sql

# Copy custom PostgreSQL configuration
COPY docker-init/db/postgresql.conf /etc/postgresql/postgresql.conf

# Set default environment variables
# Use subdirectory for PGDATA to avoid "lost+found" issue on mounted volumes
ENV POSTGRES_USER=postgres \
    POSTGRES_DB=insforge \
    PGDATA=/var/lib/postgresql/data/pgdata

# Create entrypoint script that passes encryption key to postgres
# This allows ENCRYPTION_KEY to be set via environment variable at runtime
COPY <<'EOF' /usr/local/bin/docker-entrypoint-wrapper.sh
#!/bin/bash
set -e

# Use ENCRYPTION_KEY if set, otherwise fall back to JWT_SECRET, then default
ENCRYPTION_KEY="${ENCRYPTION_KEY:-${JWT_SECRET:-dev-secret-please-change-in-production}}"

# Execute postgres with the encryption key parameter
exec docker-entrypoint.sh postgres \
    -c config_file=/etc/postgresql/postgresql.conf \
    -c "app.encryption_key=${ENCRYPTION_KEY}"
EOF

RUN chmod +x /usr/local/bin/docker-entrypoint-wrapper.sh

# Use wrapper script as entrypoint
ENTRYPOINT ["/usr/local/bin/docker-entrypoint-wrapper.sh"]
CMD []
