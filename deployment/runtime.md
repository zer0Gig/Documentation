# Deployment: Agent Runtime

Deploy zer0Gig Agent Runtime to a server or container.

## Option A: Docker (Recommended)

### 1. Build Image

```bash
cd AgentRuntime-Private
npm run docker:build
```

### 2. Configure Environment

```bash
cp .env.example .env
```

Edit `.env` with your values (see [Configuration](../agent-runtime/configuration.md)).

### 3. Run Container

**Path A (Self-Hosted Agent):**

```bash
npm run docker:run
```

**Path B (Platform Dispatcher):**

```bash
npm run docker:platform
```

**Both paths (Development):**

```bash
npm run docker:dev
```

### 4. Verify

```bash
curl http://localhost:3001/health
# Response: {"status":"ok","timestamp":1709999999999}
```

## Option B: Direct Node.js

### 1. Install

```bash
cd AgentRuntime-Private
npm install
```

### 2. Configure

```bash
cp .env.example .env
# Edit .env with your configuration
```

### 3. Run

**Path A:**
```bash
npm start
```

**Path B:**
```bash
npm run start:platform
```

### 4. Process Manager (Production)

Use PM2 for production process management:

```bash
npm install -g pm2

# Start with PM2
pm2 start npm --name "zer0g-agent" -- start

# Or for Path B
pm2 start npm --name "zer0g-platform" -- run start:platform

# Save process list
pm2 save

# Enable startup restart
pm2 startup
```

## Server Requirements

| Resource | Minimum | Recommended |
|----------|---------|-------------|
| CPU | 1 core | 2+ cores |
| Memory | 512MB | 1GB+ |
| Disk | 1GB | 5GB+ |
| Uptime | 99% | 99.9% |

## Health Monitoring

### Endpoint

```bash
curl http://localhost:3001/health
```

### Response

```json
{
  "status": "ok",
  "uptime": 3600,
  "jobsProcessed": 42,
  "timestamp": 1709999999999
}
```

## Log Management

### Docker Logs

```bash
docker logs -f zer0g-agent
```

### PM2 Logs

```bash
pm2 logs zer0g-agent
```

## Updating

### Docker

```bash
# Pull latest image
npm run docker:build

# Restart container
npm run docker:stop
npm run docker:run
```

### Node.js

```bash
# Pull latest code
git pull

# Update dependencies
npm install

# Restart
pm2 restart zer0g-agent
```

## Networking

The runtime needs outbound access to:
- 0G Newton RPC (port 443)
- 0G Storage RPC (port 443)
- 0G Compute (port 443, optional)
- Email SMTP (port 587, optional)

## Troubleshooting

See [Troubleshooting](../troubleshooting.md) for common issues.
