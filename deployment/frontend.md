# Deployment: Frontend

Deploy zer0Gig frontend to production.

## Option A: Vercel (Recommended)

### 1. Install Vercel CLI

```bash
npm install -g vercel
```

### 2. Deploy

```bash
cd Frontend-Private
npx vercel --prod
```

### 3. Set Environment Variables

In Vercel dashboard, add:

| Variable | Value |
|----------|-------|
| `NEXT_PUBLIC_PRIVY_APP_ID` | Your Privy App ID |

### 4. Configure Domain (Optional)

Custom domain can be set in Vercel dashboard.

## Option B: Docker

### 1. Build

```bash
cd Frontend-Private
docker build -t zer0g-frontend .
```

### 2. Run

```bash
docker run -p 3000:3000 \
  -e NEXT_PUBLIC_PRIVY_APP_ID=your_id \
  zer0g-frontend
```

## Option C: Self-Hosted

### 1. Build

```bash
cd Frontend-Private
npm install
npm run build
```

### 2. Start

```bash
npm start
# Runs on http://localhost:3000
```

### 3. Process Manager

```bash
npm install -g pm2
pm2 start npm --name "zer0g-frontend" -- start
```

## Environment Variables

### Required

| Variable | Description |
|----------|-------------|
| `NEXT_PUBLIC_PRIVY_APP_ID` | Privy authentication App ID |

### Optional

| Variable | Description |
|----------|-------------|
| `NEXT_PUBLIC_WC_PROJECT_ID` | WalletConnect project ID |

## Post-Deployment

### 1. Update Contract Addresses

Ensure `src/lib/contracts.ts` has production contract addresses.

### 2. Verify 0G Integration

Test on-chain interactions:
- Connect wallet
- Register as Client/Agent Owner
- Create a test job
- Verify on [0G Explorer](https://explorer.0g.ai)

### 3. Monitor

- Check browser console for errors
- Monitor wallet transactions
- Verify all pages load correctly

## Performance

### Optimization

Next.js automatically optimizes:
- Code splitting
- Image optimization
- Font optimization

### Caching

- Static assets cached at CDN edge
- API responses use cache headers
- Contract calls cached via TanStack Query

## Troubleshooting

### Build Fails

Check:
- All dependencies installed (`npm install`)
- TypeScript compiles (`npm run build`)
- Environment variables set

### Runtime Errors

Check:
- Privy App ID is valid
- Contract addresses correct
- Wallet connected to correct network
