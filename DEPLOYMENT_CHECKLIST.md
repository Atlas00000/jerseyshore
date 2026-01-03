# Deployment Checklist

Quick reference checklist for deploying the Shirt Configurator application.

## Pre-Deployment

- [ ] All code committed and pushed to GitHub
- [ ] Environment variables documented
- [ ] Cloudflare R2 bucket configured and accessible
- [ ] 3D models uploaded to R2 bucket

## Railway (Server & Database)

### Setup
- [ ] Create Railway account
- [ ] Create new project
- [ ] Connect GitHub repository (`jerseyshore_server`)
- [ ] Add PostgreSQL database service
- [ ] Configure server service

### Environment Variables
- [ ] `PORT=3001`
- [ ] `NODE_ENV=production`
- [ ] `DATABASE_URL` (from Railway PostgreSQL service)
- [ ] `CLOUDFLARE_ACCOUNT_ID`
- [ ] `CLOUDFLARE_R2_ACCESS_KEY_ID`
- [ ] `CLOUDFLARE_R2_SECRET_ACCESS_KEY`
- [ ] `CLOUDFLARE_R2_BUCKET_NAME`
- [ ] `CLOUDFLARE_R2_PUBLIC_URL`
- [ ] `CORS_ORIGIN` (update after Vercel deployment)
- [ ] `LOG_DIR=/app/logs`
- [ ] `LOG_FORMAT=json`
- [ ] `ENABLE_FILE_LOGGING=true`

### Deployment
- [ ] Verify build command: `pnpm build`
- [ ] Verify start command: `pnpm start`
- [ ] Trigger deployment
- [ ] Monitor build logs
- [ ] Generate public domain
- [ ] Test health endpoint: `https://your-server.up.railway.app/health`
- [ ] Save server URL for client configuration

## Vercel (Client)

### Setup
- [ ] Create Vercel account
- [ ] Import GitHub repository (`jerseyshore_client`)
- [ ] Verify Next.js auto-detection

### Environment Variables
- [ ] `NEXT_PUBLIC_API_URL` (Railway server URL)
- [ ] `NEXT_PUBLIC_CLOUDFLARE_R2_PUBLIC_URL`
- [ ] `NEXT_PUBLIC_CLOUDFLARE_ACCOUNT_ID`
- [ ] `NEXT_PUBLIC_CLOUDFLARE_IMAGES_DELIVERY_URL` (optional)

### Deployment
- [ ] Verify build command: `pnpm build`
- [ ] Verify install command: `pnpm install`
- [ ] Trigger deployment
- [ ] Monitor build logs
- [ ] Test application at Vercel URL
- [ ] Save client URL

## Post-Deployment

### Update CORS
- [ ] Update Railway `CORS_ORIGIN` with Vercel URL
- [ ] Redeploy Railway service

### Verification
- [ ] Server health check passes
- [ ] Client loads without errors
- [ ] 3D viewer displays correctly
- [ ] API calls work from client
- [ ] No CORS errors in browser console
- [ ] Models load from Cloudflare R2

### Monitoring
- [ ] Set up Railway monitoring/alerts
- [ ] Set up Vercel analytics
- [ ] Configure error tracking (if applicable)

## Troubleshooting Quick Fixes

### Server won't start
- Check Railway logs
- Verify all environment variables are set
- Check database connection

### Client build fails
- Check Vercel build logs
- Verify all `NEXT_PUBLIC_*` variables are set
- Check for TypeScript errors

### CORS errors
- Verify `CORS_ORIGIN` matches Vercel URL exactly
- Include `https://` protocol
- No trailing slash

### API calls fail
- Verify `NEXT_PUBLIC_API_URL` is correct
- Check server is running
- Verify CORS configuration

---

**See [DEPLOYMENT.md](./DEPLOYMENT.md) for detailed instructions.**

