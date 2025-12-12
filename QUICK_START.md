# Quick Start Deployment Guide

This is a condensed version of the full deployment guide. For detailed instructions, see [DEPLOYMENT.md](./DEPLOYMENT.md).

## 🚀 Quick Deploy Steps

### 1. Backend on Koyeb (5 minutes)

1. **Push to GitHub** (if not already done)
2. **Go to [Koyeb Dashboard](https://app.koyeb.com)**
3. **Create New App** → Connect GitHub → Select your repo
4. **Configure:**
   - Dockerfile Path: `backend/Dockerfile`
   - Build Context: `.` (root)
5. **Add Secrets** (Settings → Secrets):
   - `BREVO_API_KEY`
   - `GROQ_API_KEY`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `SUPABASE_SERVICE_ROLE_KEY`
   - `GOOGLE_APP_PASSWORD` (optional)
6. **Add Environment Variables:**
   - `PORT` = `8000`
   - `MFA_DEBUG_MODE` = `true`
   - `FROM_EMAIL` = `your-email@example.com`
   - `GOOGLE_EMAIL` = `your-email@gmail.com`
   - `NEXT_PUBLIC_SUPABASE_URL` = `https://your-project.supabase.co`
7. **Deploy** → Copy backend URL

### 2. Frontend on Vercel (5 minutes)

1. **Go to [Vercel Dashboard](https://vercel.com)**
2. **Import Project** → Select your GitHub repo
3. **Configure:**
   - Root Directory: `frontend` ⚠️ **IMPORTANT!**
   - Framework: Next.js (auto-detected)
4. **Add Environment Variables:**
   - `NEXT_PUBLIC_SUPABASE_URL` = `https://your-project.supabase.co`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY` = `your-anon-key`
   - `NEXT_PUBLIC_BACKEND_URL` = `https://your-backend.koyeb.app` (from step 1)
   - `BACKEND_URL` = `https://your-backend.koyeb.app` (same as above)
   - `SUPABASE_SERVICE_ROLE_KEY` = `your-service-role-key`
5. **Deploy** → Done! 🎉

## ✅ Verify Deployment

1. **Test Backend:**
   ```bash
   curl https://your-backend.koyeb.app/api/health
   # Should return: {"status":"ok"}
   ```

2. **Test Frontend:**
   - Visit your Vercel URL
   - Try signing up/signing in
   - Upload a PDF and test chat

## 🔧 Common Issues

**Backend not accessible?**
- Check Koyeb deployment logs
- Verify environment variables are set
- Test health endpoint directly

**Frontend can't connect to backend?**
- Verify `NEXT_PUBLIC_BACKEND_URL` matches your Koyeb URL
- Check browser console for errors
- Ensure backend is deployed and running

**Build fails?**
- Check root directory is set to `frontend` in Vercel
- Verify all environment variables are set
- Check build logs for specific errors

## 📝 Environment Variables Checklist

### Backend (Koyeb)
- [ ] `PORT` = `8000`
- [ ] `BREVO_API_KEY` (secret)
- [ ] `GROQ_API_KEY` (secret)
- [ ] `NEXT_PUBLIC_SUPABASE_URL`
- [ ] `NEXT_PUBLIC_SUPABASE_ANON_KEY` (secret)
- [ ] `SUPABASE_SERVICE_ROLE_KEY` (secret)
- [ ] `FROM_EMAIL`
- [ ] `MFA_DEBUG_MODE` = `true`

### Frontend (Vercel)
- [ ] `NEXT_PUBLIC_SUPABASE_URL`
- [ ] `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- [ ] `NEXT_PUBLIC_BACKEND_URL` = Your Koyeb backend URL
- [ ] `BACKEND_URL` = Your Koyeb backend URL
- [ ] `SUPABASE_SERVICE_ROLE_KEY` (server-side only)

## 🆘 Need Help?

See the full [DEPLOYMENT.md](./DEPLOYMENT.md) guide for detailed instructions and troubleshooting.

