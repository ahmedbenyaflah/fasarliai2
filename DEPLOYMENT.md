# Deployment Guide: FasarliAI on Koyeb & Vercel

This guide will help you deploy the FasarliAI application:
- **Backend (FastAPI)** → Deploy on **Koyeb**
- **Frontend (Next.js)** → Deploy on **Vercel**

---

## Prerequisites

1. **Accounts Required:**
   - [Koyeb Account](https://www.koyeb.com/) (free tier available)
   - [Vercel Account](https://vercel.com/) (free tier available)
   - [Supabase Account](https://supabase.com/) (for database & auth)
   - [GitHub Account](https://github.com/) (for repository hosting)

2. **API Keys & Secrets:**
   - Supabase URL and keys
   - GROQ API Key (for LLM)
   - Brevo API Key (for email)
   - Google App Password (optional, for email)

---

## Part 1: Deploy Backend on Koyeb

### Step 1: Prepare Your Repository

1. Push your code to GitHub (if not already done):
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
   git push -u origin main
   ```

### Step 2: Fix Dockerfile Context

The Dockerfile expects to be run from the project root. Update it if needed:

**Current Dockerfile is correct** - it copies from `backend/` directory, which is fine when building from the root.

### Step 3: Deploy on Koyeb

#### Option A: Using Koyeb Dashboard (Recommended)

1. **Sign in to Koyeb:**
   - Go to [https://app.koyeb.com](https://app.koyeb.com)
   - Sign in or create an account

2. **Create a New App:**
   - Click "Create App" or "New App"
   - Select "GitHub" as your source
   - Authorize Koyeb to access your GitHub account
   - Select your repository

3. **Configure Build Settings:**
   - **Build Command:** Leave empty (Docker build)
   - **Dockerfile Path:** `backend/Dockerfile`
   - **Docker Build Context:** `.` (project root)

4. **Configure Environment Variables:**
   Add the following secrets in Koyeb:
   
   Go to **Settings → Secrets** and create these secrets:
   - `BREVO_API_KEY` - Your Brevo/Sendinblue API key
   - `GROQ_API_KEY` - Your Groq API key
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY` - Supabase anonymous key
   - `SUPABASE_SERVICE_ROLE_KEY` - Supabase service role key
   - `GOOGLE_APP_PASSWORD` - Google app password (if using Gmail)

   Then in **Settings → Environment Variables**, add:
   - `PORT` = `8000`
   - `MFA_DEBUG_MODE` = `true` (or `false` for production)
   - `FROM_EMAIL` = `your-email@example.com`
   - `GOOGLE_EMAIL` = `your-email@gmail.com`
   - `NEXT_PUBLIC_SUPABASE_URL` = `https://your-project.supabase.co`

5. **Deploy:**
   - Click "Deploy"
   - Wait for the build to complete
   - Copy your app URL (e.g., `https://your-app-name.koyeb.app`)

#### Option B: Using Koyeb CLI

1. **Install Koyeb CLI:**
   ```bash
   # Windows (PowerShell)
   iwr https://github.com/koyeb/koyeb-cli/releases/latest/download/koyeb-cli_windows_amd64.exe -OutFile koyeb.exe
   
   # Or download from: https://github.com/koyeb/koyeb-cli/releases
   ```

2. **Login:**
   ```bash
   koyeb auth login
   ```

3. **Create Service:**
   ```bash
   koyeb service create \
     --name fasarliai-backend \
     --git github.com/YOUR_USERNAME/YOUR_REPO \
     --git-branch main \
     --git-build-command "" \
     --git-dockerfile backend/Dockerfile \
     --ports 8000:http \
     --env PORT=8000 \
     --env MFA_DEBUG_MODE=true \
     --env FROM_EMAIL=your-email@example.com \
     --env GOOGLE_EMAIL=your-email@gmail.com \
     --env NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co \
     --secret BREVO_API_KEY \
     --secret GROQ_API_KEY \
     --secret NEXT_PUBLIC_SUPABASE_ANON_KEY \
     --secret SUPABASE_SERVICE_ROLE_KEY \
     --secret GOOGLE_APP_PASSWORD
   ```

### Step 4: Get Your Backend URL

After deployment, note your backend URL:
- Format: `https://your-app-name.koyeb.app`
- Or: `https://fasarliai-backend-xxxxx.koyeb.app`

**Important:** Save this URL - you'll need it for the frontend deployment!

---

## Part 2: Deploy Frontend on Vercel

### Step 1: Prepare Environment Variables

Create a `.env.local` file in the `frontend` directory (for local testing):

```env
# Supabase Configuration
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key

# Backend API URL (from Koyeb)
NEXT_PUBLIC_BACKEND_URL=https://your-app-name.koyeb.app

# Server-side only (for API routes)
BACKEND_URL=https://your-app-name.koyeb.app
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
```

### Step 2: Deploy on Vercel

#### Option A: Using Vercel Dashboard (Recommended)

1. **Sign in to Vercel:**
   - Go to [https://vercel.com](https://vercel.com)
   - Sign in with GitHub

2. **Import Project:**
   - Click "Add New..." → "Project"
   - Import your GitHub repository
   - Select the repository

3. **Configure Project:**
   - **Framework Preset:** Next.js (auto-detected)
   - **Root Directory:** `frontend` (IMPORTANT!)
   - **Build Command:** `npm run build` (or leave default)
   - **Output Directory:** `.next` (default)
   - **Install Command:** `npm install` (default)

4. **Add Environment Variables:**
   
   In the "Environment Variables" section, add:
   
   **For Production, Preview, and Development:**
   - `NEXT_PUBLIC_SUPABASE_URL` = `https://your-project.supabase.co`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY` = `your-anon-key`
   - `NEXT_PUBLIC_BACKEND_URL` = `https://your-app-name.koyeb.app` (your Koyeb backend URL)
   
   **For Production and Preview only (server-side):**
   - `BACKEND_URL` = `https://your-app-name.koyeb.app`
   - `SUPABASE_SERVICE_ROLE_KEY` = `your-service-role-key`

5. **Deploy:**
   - Click "Deploy"
   - Wait for the build to complete
   - Your app will be live at `https://your-app-name.vercel.app`

#### Option B: Using Vercel CLI

1. **Install Vercel CLI:**
   ```bash
   npm i -g vercel
   ```

2. **Login:**
   ```bash
   vercel login
   ```

3. **Navigate to frontend directory:**
   ```bash
   cd frontend
   ```

4. **Deploy:**
   ```bash
   vercel
   ```
   
   Follow the prompts:
   - Link to existing project? No (first time)
   - Project name: `fasarliai` (or your choice)
   - Directory: `./` (current directory)
   - Override settings? No

5. **Add Environment Variables:**
   ```bash
   vercel env add NEXT_PUBLIC_SUPABASE_URL
   vercel env add NEXT_PUBLIC_SUPABASE_ANON_KEY
   vercel env add NEXT_PUBLIC_BACKEND_URL
   vercel env add BACKEND_URL
   vercel env add SUPABASE_SERVICE_ROLE_KEY
   ```

6. **Redeploy with environment variables:**
   ```bash
   vercel --prod
   ```

### Step 3: Update CORS Settings (if needed)

The backend already has CORS configured to allow all origins. If you encounter CORS issues:

1. Check that `NEXT_PUBLIC_BACKEND_URL` in Vercel matches your Koyeb backend URL
2. Verify the backend is running and accessible
3. Check browser console for CORS errors

---

## Part 3: Post-Deployment Checklist

### ✅ Backend (Koyeb)

- [ ] Backend is accessible at the Koyeb URL
- [ ] Health check endpoint works: `https://your-backend.koyeb.app/api/health`
- [ ] All environment variables are set correctly
- [ ] Secrets are configured in Koyeb dashboard
- [ ] Backend logs show no errors

### ✅ Frontend (Vercel)

- [ ] Frontend is accessible at the Vercel URL
- [ ] Environment variables are set in Vercel dashboard
- [ ] `NEXT_PUBLIC_BACKEND_URL` points to your Koyeb backend
- [ ] Frontend can connect to backend (check browser console)
- [ ] Authentication works (Supabase)
- [ ] File uploads work
- [ ] Chat functionality works

### ✅ Testing

1. **Test Backend:**
   ```bash
   curl https://your-backend.koyeb.app/api/health
   # Should return: {"status":"ok"}
   ```

2. **Test Frontend:**
   - Visit your Vercel URL
   - Try signing up/signing in
   - Upload a PDF
   - Send a chat message
   - Generate quiz/flashcards

---

## Troubleshooting

### Backend Issues

**Problem: Build fails on Koyeb**
- Check Dockerfile path is correct: `backend/Dockerfile`
- Verify build context is set to project root: `.`
- Check Koyeb build logs for specific errors

**Problem: Backend returns 500 errors**
- Check environment variables are set correctly
- Verify all secrets are configured
- Check Koyeb logs: `koyeb logs your-service-name`

**Problem: CORS errors**
- Backend already allows all origins (`allow_origins=["*"]`)
- Verify frontend URL is correct

### Frontend Issues

**Problem: Build fails on Vercel**
- Check root directory is set to `frontend`
- Verify `package.json` exists in frontend directory
- Check build logs for specific errors

**Problem: Cannot connect to backend**
- Verify `NEXT_PUBLIC_BACKEND_URL` is set correctly
- Check backend is running and accessible
- Test backend URL directly in browser: `https://your-backend.koyeb.app/api/health`

**Problem: Environment variables not working**
- Remember: `NEXT_PUBLIC_*` variables are exposed to client
- `BACKEND_URL` and `SUPABASE_SERVICE_ROLE_KEY` are server-only
- Redeploy after adding environment variables

**Problem: API routes return 500 errors**
- Check `SUPABASE_SERVICE_ROLE_KEY` is set (for server-side operations)
- Verify `BACKEND_URL` is set correctly
- Check Vercel function logs

---

## Environment Variables Reference

### Backend (Koyeb) - Required

| Variable | Description | Example |
|----------|-------------|---------|
| `PORT` | Server port | `8000` |
| `BREVO_API_KEY` | Brevo email API key | (secret) |
| `GROQ_API_KEY` | Groq LLM API key | (secret) |
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase project URL | `https://xxx.supabase.co` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase anonymous key | (secret) |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase service role key | (secret) |
| `FROM_EMAIL` | Sender email address | `noreply@example.com` |
| `MFA_DEBUG_MODE` | Enable debug mode | `true` or `false` |

### Frontend (Vercel) - Required

| Variable | Description | Example |
|----------|-------------|---------|
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase project URL | `https://xxx.supabase.co` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase anonymous key | `eyJ...` |
| `NEXT_PUBLIC_BACKEND_URL` | Backend API URL (Koyeb) | `https://xxx.koyeb.app` |
| `BACKEND_URL` | Backend API URL (server-side) | `https://xxx.koyeb.app` |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase service role key | (secret, server-only) |

---

## Cost Estimation

### Free Tier Limits

**Koyeb:**
- Free tier: 1 service, 512MB RAM, shared CPU
- Sufficient for small to medium traffic

**Vercel:**
- Free tier: Unlimited projects, 100GB bandwidth/month
- Perfect for Next.js applications

**Supabase:**
- Free tier: 500MB database, 1GB file storage
- Upgrade as needed

---

## Next Steps

1. **Set up custom domains** (optional):
   - Koyeb: Add custom domain in settings
   - Vercel: Add custom domain in project settings

2. **Enable monitoring:**
   - Set up error tracking (Sentry, etc.)
   - Monitor Koyeb and Vercel logs

3. **Optimize performance:**
   - Enable Vercel Analytics
   - Monitor backend response times
   - Optimize database queries

4. **Security:**
   - Review CORS settings
   - Rotate API keys regularly
   - Enable Supabase RLS policies

---

## Support

If you encounter issues:
1. Check the troubleshooting section above
2. Review Koyeb/Vercel build logs
3. Check browser console for frontend errors
4. Verify all environment variables are set correctly

Good luck with your deployment! 🚀

