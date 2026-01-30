# InsForge Deploy Provider

One-click deploy InsForge to third party platforms.

## Deploy to Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/template/insforge?referralCode=insforge)

### What Gets Deployed

| Service | Description |
|---------|-------------|
| **PostgreSQL** | Custom image with pgvector, pg_cron, pg_net, pgcrypto extensions |
| **PostgREST** | Instant REST API from your PostgreSQL schema |
| **InsForge** | Main application server |
| **Deno Runtime** | Serverless function execution environment |

### Environment Variables

After deployment, the following variables are automatically configured:

| Variable | Description |
|----------|-------------|
| `JWT_SECRET` | Auto-generated secret for JWT tokens |
| `ENCRYPTION_KEY` | Auto-generated key for encrypting secrets |
| `ADMIN_EMAIL` | Admin user email (default: admin@example.com) |
| `ADMIN_PASSWORD` | Auto-generated admin password |
| `POSTGRES_PASSWORD` | Auto-generated database password |

### Optional Configuration

You can configure these optional features after deployment:

**OAuth Providers:**
- `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET`
- `GITHUB_CLIENT_ID` / `GITHUB_CLIENT_SECRET`

**Deno Subhosting:**
- `DENO_SUBHOSTING_TOKEN`
- `DENO_SUBHOSTING_ORG_ID`

**AWS S3 Storage:**
- `AWS_S3_BUCKET`
- `AWS_REGION`

### Post-Deployment Steps

1. Navigate to your InsForge service in Railway
2. Find the generated `ADMIN_PASSWORD` in environment variables
3. Access your app via the public domain Railway provides
4. Login with your admin credentials

## Deploy with Docker Compose (Self-Hosted)

For self-hosted deployments, use Docker Compose:

```bash
# Clone the repository
git clone https://github.com/insforge/deploy-provider.git
cd deploy-provider

# Create .env file with your secrets
cp .env.example .env
# Edit .env with your values

# Start the stack
docker-compose up -d
```

### Required Environment Variables

Create a `.env` file with:

```env
# Security (REQUIRED - change these!)
JWT_SECRET=your-super-secret-jwt-key-at-least-32-chars
ENCRYPTION_KEY=your-encryption-key-32-chars

# Admin Credentials
ADMIN_EMAIL=admin@yourdomain.com
ADMIN_PASSWORD=your-secure-admin-password

# Database (optional, has defaults)
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your-db-password
POSTGRES_DB=insforge
```

## Architecture

```
                    ┌─────────────────┐
                    │   Client/Web    │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │    InsForge     │ :7130
                    │  (Main App)     │
                    └────────┬────────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
┌───────▼───────┐   ┌────────▼────────┐   ┌───────▼───────┐
│   PostgREST   │   │      Deno       │   │   PostgreSQL  │
│  (REST API)   │   │   (Functions)   │   │  (Database)   │
│    :5430      │   │     :7133       │   │    :5432      │
└───────┬───────┘   └────────┬────────┘   └───────────────┘
        │                    │                    ▲
        └────────────────────┴────────────────────┘
```

## License

MIT
