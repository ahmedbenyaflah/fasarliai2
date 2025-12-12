# Deployment Setup Summary

## Files Created/Modified

### ✅ Created Files

1. **DEPLOYMENT.md** - Comprehensive deployment guide with step-by-step instructions
2. **QUICK_START.md** - Condensed quick reference guide
3. **frontend/vercel.json** - Vercel configuration file

### ✅ Modified Files

1. **backend/koyeb.yaml** - Fixed Dockerfile path from `Dockerfile` to `backend/Dockerfile`

## Deployment Architecture

```
┌─────────────────┐         ┌─────────────────┐
│   Frontend      │         │    Backend      │
│   (Next.js)     │────────▶│   (FastAPI)     │
│   on Vercel     │  HTTP   │   on Koyeb      │
└─────────────────┘         └─────────────────┘
         │                           │
         │                           │
         └───────────┬───────────────┘
                     │
                     ▼
              ┌──────────────┐
              │   Supabase   │
              │  (Database)  │
              └──────────────┘
```

## Key Configuration Points

### Backend (Koyeb)
- **Dockerfile Path:** `backend/Dockerfile`
- **Build Context:** `.` (project root)
- **Port:** `8000`
- **Region:** `fra` (Frankfurt) - can be changed in koyeb.yaml

### Frontend (Vercel)
- **Root Directory:** `frontend` ⚠️ **CRITICAL**
- **Framework:** Next.js (auto-detected)
- **Build Command:** `npm run build` (default)

## Environment Variables Required

### Backend Secrets (Koyeb)
- `BREVO_API_KEY`
- `GROQ_API_KEY`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`
- `GOOGLE_APP_PASSWORD` (optional)

### Backend Environment Variables (Koyeb)
- `PORT` = `8000`
- `MFA_DEBUG_MODE` = `true` or `false`
- `FROM_EMAIL` = Your email
- `GOOGLE_EMAIL` = Your Gmail (if using)
- `NEXT_PUBLIC_SUPABASE_URL` = Your Supabase URL

### Frontend Environment Variables (Vercel)
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `NEXT_PUBLIC_BACKEND_URL` = Your Koyeb backend URL
- `BACKEND_URL` = Your Koyeb backend URL (server-side)
- `SUPABASE_SERVICE_ROLE_KEY` (server-side only)

## Next Steps

1. **Read the deployment guide:**
   - Full guide: [DEPLOYMENT.md](./DEPLOYMENT.md)
   - Quick start: [QUICK_START.md](./QUICK_START.md)

2. **Prepare your environment:**
   - Get all API keys ready
   - Have Supabase project set up
   - Push code to GitHub

3. **Deploy:**
   - Deploy backend on Koyeb first
   - Get backend URL
   - Deploy frontend on Vercel with backend URL

4. **Test:**
   - Verify backend health endpoint
   - Test frontend functionality
   - Check all features work

## Important Notes

⚠️ **Remember:**
- Frontend root directory MUST be set to `frontend` in Vercel
- Backend Dockerfile path is `backend/Dockerfile` in Koyeb
- `NEXT_PUBLIC_BACKEND_URL` must match your Koyeb backend URL
- All secrets should be added as "Secrets" in Koyeb, not plain environment variables
- Server-side variables (`BACKEND_URL`, `SUPABASE_SERVICE_ROLE_KEY`) should NOT have `NEXT_PUBLIC_` prefix

## Support Resources

- [Koyeb Documentation](https://www.koyeb.com/docs)
- [Vercel Documentation](https://vercel.com/docs)
- [Supabase Documentation](https://supabase.com/docs)

Good luck with your deployment! 🚀

