# Deployment Guide

This guide covers deploying the Shirt Configurator application to production:
- **Server & Database**: Railway
- **Client**: Vercel

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Server & Database Deployment (Railway)](#server--database-deployment-railway)
3. [Client Deployment (Vercel)](#client-deployment-vercel)
4. [Environment Variables](#environment-variables)
5. [Post-Deployment Verification](#post-deployment-verification)
6. [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before deploying, ensure you have:

- [ ] Railway account ([railway.app](https://railway.app))
- [ ] Vercel account ([vercel.com](https://vercel.com))
- [ ] Cloudflare R2 account with bucket configured
- [ ] GitHub repository access (for automatic deployments)
- [ ] Environment variables documented and ready

---

## Server & Database Deployment (Railway)

### Step 1: Create Railway Project

1. Go to [Railway Dashboard](https://railway.app/dashboard)
2. Click **"New Project"**
3. Select **"Deploy from GitHub repo"**
4. Choose your repository: `Atlas00000/jerseyshore_server`
5. Railway will automatically detect the Node.js project

### Step 2: Add PostgreSQL Database

1. In your Railway project, click **"+ New"**
2. Select **"Database"** → **"Add PostgreSQL"**
3. Railway will automatically create a PostgreSQL database
4. Note the connection details (you'll need these for environment variables)

### Step 3: Configure Server Service

1. Railway should have automatically created a service from your GitHub repo
2. If not, click **"+ New"** → **"GitHub Repo"** → Select `jerseyshore_server`
3. Railway will detect the `nixpacks.toml` configuration

### Step 4: Set Environment Variables

In the Railway service settings, add the following environment variables:

#### Required Variables

```bash
# Server Configuration
PORT=3001
NODE_ENV=production

# Database (Auto-provided by Railway PostgreSQL service)
DATABASE_URL=${{Postgres.DATABASE_URL}}

# Redis (Optional - if using Redis service)
REDIS_URL=${{Redis.REDIS_URL}}

# Cloudflare R2 Configuration
CLOUDFLARE_ACCOUNT_ID=your_account_id
CLOUDFLARE_R2_ACCESS_KEY_ID=your_access_key_id
CLOUDFLARE_R2_SECRET_ACCESS_KEY=your_secret_access_key
CLOUDFLARE_R2_BUCKET_NAME=threejs-assets
CLOUDFLARE_R2_PUBLIC_URL=https://pub-42d9986d97a0490598cb89136641b713.r2.dev

# CORS (Update with your Vercel domain after client deployment)
CORS_ORIGIN=https://your-app.vercel.app

# Logging
LOG_DIR=/app/logs
LOG_FORMAT=json
ENABLE_FILE_LOGGING=true
```

#### How to Get Railway Database URL

1. Click on your PostgreSQL service in Railway
2. Go to the **"Variables"** tab
3. Copy the `DATABASE_URL` value
4. Use it in your server service environment variables

### Step 5: Configure Build Settings

Railway should automatically detect:
- **Build Command**: `pnpm build`
- **Start Command**: `pnpm start`
- **Root Directory**: `/` (root of server repo)

If needed, verify in **Settings** → **Deploy**:
- Build Command: `pnpm build`
- Start Command: `pnpm start`

### Step 6: Deploy

1. Railway will automatically deploy on every push to the `main` branch
2. Or manually trigger deployment: **"Deploy"** → **"Redeploy"**
3. Monitor the deployment logs in the **"Deployments"** tab

### Step 7: Get Server URL

1. After successful deployment, go to **Settings** → **Networking**
2. Click **"Generate Domain"** to create a public URL
3. Copy the domain (e.g., `your-server.up.railway.app`)
4. Save this URL - you'll need it for the client deployment

---

## Client Deployment (Vercel)

### Step 1: Import Project to Vercel

1. Go to [Vercel Dashboard](https://vercel.com/dashboard)
2. Click **"Add New..."** → **"Project"**
3. Import your GitHub repository: `Atlas00000/jerseyshore_client`
4. Vercel will automatically detect Next.js

### Step 2: Configure Project Settings

In the project configuration:

- **Framework Preset**: Next.js (auto-detected)
- **Root Directory**: `/` (root of client repo)
- **Build Command**: `pnpm build` (or leave default)
- **Output Directory**: `.next` (default)
- **Install Command**: `pnpm install`

### Step 3: Set Environment Variables

Add the following environment variables in **Settings** → **Environment Variables**:

#### Required Variables

```bash
# API Configuration (Use your Railway server URL)
NEXT_PUBLIC_API_URL=https://your-server.up.railway.app

# Cloudflare R2 Configuration
NEXT_PUBLIC_CLOUDFLARE_R2_PUBLIC_URL=https://pub-42d9986d97a0490598cb89136641b713.r2.dev
NEXT_PUBLIC_CLOUDFLARE_ACCOUNT_ID=421f31ce5d60990daad73b9c9448d1c8

# Optional: Cloudflare Images
NEXT_PUBLIC_CLOUDFLARE_IMAGES_DELIVERY_URL=https://imagedelivery.net
```

**Important**: 
- All `NEXT_PUBLIC_*` variables are exposed to the browser
- Never put sensitive keys in `NEXT_PUBLIC_*` variables
- Update `NEXT_PUBLIC_API_URL` with your actual Railway server URL

### Step 4: Deploy

1. Click **"Deploy"**
2. Vercel will build and deploy your application
3. Monitor the build logs for any errors
4. Once complete, you'll get a deployment URL (e.g., `your-app.vercel.app`)

### Step 5: Update CORS in Server

After getting your Vercel domain:

1. Go back to Railway server service
2. Update the `CORS_ORIGIN` environment variable:
   ```bash
   CORS_ORIGIN=https://your-app.vercel.app
   ```
3. Redeploy the server service

### Step 6: Configure Custom Domain (Optional)

1. In Vercel project settings, go to **"Domains"**
2. Add your custom domain
3. Follow DNS configuration instructions
4. Update `CORS_ORIGIN` in Railway to include your custom domain

---

## Environment Variables

### Server Environment Variables (Railway)

| Variable | Description | Required | Example |
|----------|-------------|----------|---------|
| `PORT` | Server port | Yes | `3001` |
| `NODE_ENV` | Environment | Yes | `production` |
| `DATABASE_URL` | PostgreSQL connection | Yes | `postgresql://...` |
| `REDIS_URL` | Redis connection | No | `redis://...` |
| `CLOUDFLARE_ACCOUNT_ID` | Cloudflare account ID | Yes | `421f31ce5d60990daad73b9c9448d1c8` |
| `CLOUDFLARE_R2_ACCESS_KEY_ID` | R2 access key | Yes | `your_key` |
| `CLOUDFLARE_R2_SECRET_ACCESS_KEY` | R2 secret key | Yes | `your_secret` |
| `CLOUDFLARE_R2_BUCKET_NAME` | R2 bucket name | Yes | `threejs-assets` |
| `CLOUDFLARE_R2_PUBLIC_URL` | R2 public URL | Yes | `https://pub-...r2.dev` |
| `CORS_ORIGIN` | Allowed origin | Yes | `https://your-app.vercel.app` |
| `LOG_DIR` | Log directory | No | `/app/logs` |
| `LOG_FORMAT` | Log format | No | `json` |
| `ENABLE_FILE_LOGGING` | Enable file logs | No | `true` |

### Client Environment Variables (Vercel)

| Variable | Description | Required | Example |
|----------|-------------|----------|---------|
| `NEXT_PUBLIC_API_URL` | Server API URL | Yes | `https://your-server.up.railway.app` |
| `NEXT_PUBLIC_CLOUDFLARE_R2_PUBLIC_URL` | R2 public URL | Yes | `https://pub-...r2.dev` |
| `NEXT_PUBLIC_CLOUDFLARE_ACCOUNT_ID` | Cloudflare account ID | Yes | `421f31ce5d60990daad73b9c9448d1c8` |
| `NEXT_PUBLIC_CLOUDFLARE_IMAGES_DELIVERY_URL` | Images URL | No | `https://imagedelivery.net` |

---

## Post-Deployment Verification

### 1. Verify Server Health

```bash
curl https://your-server.up.railway.app/health
```

Expected response:
```json
{
  "status": "ok",
  "timestamp": "2026-01-03T...",
  "uptime": 123.45
}
```

### 2. Verify Client

1. Visit your Vercel deployment URL
2. Check browser console for errors
3. Test the 3D viewer functionality
4. Verify API calls are working

### 3. Check Logs

**Railway**:
- Go to your service → **"Deployments"** → Click on latest deployment → **"View Logs"**

**Vercel**:
- Go to your project → **"Deployments"** → Click on latest deployment → **"View Function Logs"**

### 4. Test API Endpoints

```bash
# Test prints endpoint
curl https://your-server.up.railway.app/api/prints

# Should return JSON response (may be empty array if no prints)
```

---

## Troubleshooting

### Server Issues

#### Build Fails
- **Issue**: Build command fails
- **Solution**: 
  - Check Railway logs for specific error
  - Verify `pnpm-lock.yaml` is committed
  - Ensure `nixpacks.toml` is correct

#### Module Not Found Errors
- **Issue**: `ERR_MODULE_NOT_FOUND` errors
- **Solution**: 
  - Verify `fix-imports.js` script ran during build
  - Check that all imports have `.js` extensions in compiled code

#### Database Connection Issues
- **Issue**: Cannot connect to database
- **Solution**:
  - Verify `DATABASE_URL` is set correctly
  - Check PostgreSQL service is running in Railway
  - Ensure database service is linked to server service

#### CORS Errors
- **Issue**: CORS errors in browser
- **Solution**:
  - Verify `CORS_ORIGIN` matches your Vercel domain exactly
  - Include protocol (`https://`)
  - No trailing slash

### Client Issues

#### Build Fails
- **Issue**: Next.js build fails
- **Solution**:
  - Check Vercel build logs
  - Verify all environment variables are set
  - Ensure `next.config.js` is correct

#### API Calls Fail
- **Issue**: Cannot connect to server
- **Solution**:
  - Verify `NEXT_PUBLIC_API_URL` is correct
  - Check server is running and accessible
  - Verify CORS is configured correctly

#### 3D Models Not Loading
- **Issue**: Models don't appear
- **Solution**:
  - Verify Cloudflare R2 public URL is correct
  - Check R2 bucket permissions
  - Verify models are uploaded to R2

### General Issues

#### Environment Variables Not Working
- **Solution**:
  - Redeploy after adding/changing variables
  - Verify variable names match exactly (case-sensitive)
  - Check for typos

#### Deployment Not Triggering
- **Solution**:
  - Verify GitHub integration is connected
  - Check branch name matches (usually `main`)
  - Manually trigger deployment if needed

---

## Continuous Deployment

Both Railway and Vercel support automatic deployments:

- **Railway**: Automatically deploys on push to `main` branch
- **Vercel**: Automatically deploys on push to `main` branch

To disable auto-deploy:
- **Railway**: Settings → Deploy → Disable "Auto Deploy"
- **Vercel**: Settings → Git → Disable "Automatic deployments"

---

## Monitoring

### Railway
- View logs in real-time: Service → Deployments → View Logs
- Monitor resource usage: Service → Metrics
- Set up alerts: Project → Settings → Notifications

### Vercel
- View function logs: Deployments → View Function Logs
- Monitor analytics: Analytics tab
- Set up alerts: Settings → Notifications

---

## Security Checklist

- [ ] All sensitive keys are in environment variables (not in code)
- [ ] `CORS_ORIGIN` is set to specific domain (not `*`)
- [ ] Database credentials are secure
- [ ] Cloudflare R2 keys are properly secured
- [ ] Production `NODE_ENV` is set to `production`
- [ ] No debug/development code in production
- [ ] API endpoints have proper error handling
- [ ] Rate limiting is configured (if needed)

---

## Support

For issues or questions:
- Railway: [docs.railway.app](https://docs.railway.app) or [Discord](https://discord.gg/railway)
- Vercel: [vercel.com/docs](https://vercel.com/docs) or [Discord](https://vercel.com/discord)

---

## Quick Reference

### Railway Server URL Format
```
https://your-service-name.up.railway.app
```

### Vercel Client URL Format
```
https://your-project.vercel.app
```

### Update CORS After Client Deployment
1. Get Vercel deployment URL
2. Update Railway `CORS_ORIGIN` variable
3. Redeploy Railway service

---

**Last Updated**: January 2026

