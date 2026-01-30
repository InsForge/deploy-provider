# Deno Serverless Runtime for InsForge
# Pre-built image for cloud deployments (Railway, Render, Fly.io, etc.)
#
# Build: docker build -t ghcr.io/insforge/deno-runtime:latest -f railway/deno/Dockerfile .
# Push:  docker push ghcr.io/insforge/deno-runtime:latest

FROM denoland/deno:alpine-2.0.6

WORKDIR /app

# Set Deno cache directory
ENV DENO_DIR=/deno-dir \
    PORT=7133

# Copy function runtime code
COPY functions/deno.json ./deno.json
COPY functions/deno.lock ./deno.lock
COPY functions/server.ts ./server.ts
COPY functions/worker-template.js ./worker-template.js

# Cache dependencies at build time for faster startup
RUN deno cache server.ts

# Create cache directory
RUN mkdir -p /deno-dir

# Expose the runtime port
EXPOSE 7133

# Run with minimal permissions:
# - --allow-net: Required for HTTP server and database connections
# - --allow-env: Required for reading configuration
# - --allow-read: Limited to worker template only for security
CMD ["deno", "run", "--allow-net", "--allow-env", "--allow-read=./worker-template.js", "server.ts"]
