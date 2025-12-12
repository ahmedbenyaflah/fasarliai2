# Fixing Docker Image Size Issue

## Problem
Your Docker image is **3428 MiB** but Koyeb free tier only allows **2000 MiB**.

## Solutions Applied

### ✅ Solution 1: Updated Instance Type (Recommended)
The `koyeb.yaml` has been updated to use `starter` instance type instead of `free`:
- **Free tier:** 2GB image limit
- **Starter tier:** 10GB image limit ✅

**Action Required:**
1. In Koyeb dashboard, go to your service
2. Settings → Instance → Change to "Starter"
3. Redeploy

### ✅ Solution 2: Optimized Dockerfile
Created a multi-stage Dockerfile that:
- Uses multi-stage builds to reduce final image size
- Removes build dependencies in final image
- Cleans up Python cache files
- Uses `.dockerignore` to exclude unnecessary files

### ✅ Solution 3: Created .dockerignore
Added `.dockerignore` to exclude:
- Frontend files
- Documentation
- Git files
- Cache files
- IDE files

## Quick Fix Steps

### Option A: Use Starter Instance (Easiest)

1. **In Koyeb Dashboard:**
   - Go to your service
   - Click "Settings"
   - Go to "Instance" section
   - Change from "Free" to "Starter"
   - Click "Save"
   - Redeploy

2. **Or update koyeb.yaml and redeploy:**
   ```yaml
   instance_type: starter  # Already updated
   ```

### Option B: Use Alpine-based Dockerfile (Smallest Size)

If you still have size issues, use the Alpine-based Dockerfile:

1. **In Koyeb Dashboard:**
   - Go to your service
   - Click "Settings"
   - Go to "Build" section
   - Change Dockerfile path to: `backend/Dockerfile.optimized`
   - Save and redeploy

**Note:** Alpine-based images are smaller but may have compatibility issues with some Python packages. Test thoroughly.

## Why Is The Image So Large?

The main contributors to image size are:
1. **sentence-transformers** - Downloads ML models (~500MB+)
2. **langchain** and dependencies (~200MB+)
3. **faiss-cpu** - Vector search library (~100MB+)
4. **Python base image** (~150MB)
5. **System dependencies** (~100MB+)

Total: ~1-2GB is normal for ML applications.

## Verification

After deploying with starter instance, check:
1. Build completes successfully
2. Service starts without errors
3. Health endpoint works: `https://your-backend.koyeb.app/api/health`

## Cost Impact

- **Free tier:** $0/month (2GB image limit)
- **Starter tier:** ~$7-10/month (10GB image limit, 1GB RAM)

The starter tier is necessary for ML applications with large dependencies.

## Alternative: Further Optimization

If you need to stay on free tier, consider:
1. Using lighter ML models
2. Lazy-loading models (download on first use)
3. Using external model hosting
4. Splitting services (separate ML service)

But for now, **using starter tier is the recommended solution**.

