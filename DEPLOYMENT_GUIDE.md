# 🚀 PetBloom Full Deployment Guide

## Quick Overview
- **Frontend**: React + Vite → Vercel
- **Backend**: FastAPI + PostgreSQL → Railway  
- **Auth**: Firebase Authentication
- **Database**: PostgreSQL (Railway)

---

## 📋 Prerequisites Checklist

- [ ] GitHub account
- [ ] Vercel account (https://vercel.com)
- [ ] Railway account (https://railway.app)
- [ ] Firebase project with credentials

---

## Part 1: Deploy Backend to Railway 🚂

### Step 1: Push Backend to GitHub

```bash
cd /home/vanessa/Development/code/se-prep/phase-4/petbloom-backend

# Create new repo on GitHub named "petbloom-backend"
# Then run:
git remote add origin git@github.com:YOUR_USERNAME/petbloom-backend.git
git commit -m "Initial backend setup for Railway deployment"
git branch -M main
git push -u origin main
```

### Step 2: Create Railway Project

1. Go to https://railway.app
2. Click **"New Project"**
3. Select **"Deploy from GitHub repo"**
4. Authorize GitHub and select `petbloom-backend`
5. Railway will start deploying automatically

### Step 3: Add PostgreSQL Database

1. In your Railway project, click **"+ New"**
2. Select **"Database"** → **"PostgreSQL"**
3. Railway automatically creates `DATABASE_URL` environment variable
4. The backend service will automatically connect to it

### Step 4: Set Environment Variables

1. Click on your backend service
2. Go to **"Variables"** tab
3. Click **"+ New Variable"** and add each:

```bash
# Database (automatically set by Railway, but verify it exists)
DATABASE_URL=<should be auto-populated>

# Frontend URL (update after deploying frontend)
FRONTEND_URL=https://your-app-name.vercel.app

# Firebase Admin SDK
FIREBASE_PROJECT_ID=your-firebase-project-id
FIREBASE_CLIENT_EMAIL=firebase-adminsdk-xxxxx@your-project.iam.gserviceaccount.com

# Firebase Private Key (IMPORTANT: Keep the quotes and \n characters)
FIREBASE_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\nYOUR_ACTUAL_PRIVATE_KEY_HERE\n-----END PRIVATE KEY-----\n"

# Port (Railway sets this automatically)
PORT=8000
```

**Getting Firebase Admin Credentials:**
1. Go to Firebase Console → Project Settings
2. Click "Service Accounts" tab
3. Click "Generate New Private Key"
4. Download the JSON file
5. Extract `project_id`, `client_email`, and `private_key`

### Step 5: Deploy & Get Backend URL

1. Railway will automatically redeploy after adding variables
2. Wait for deployment to complete (check Deployments tab)
3. Go to **"Settings"** → **"Generate Domain"**
4. Copy your backend URL (e.g., `https://petbloom-backend-production.up.railway.app`)
5. Test it: Open `https://YOUR-BACKEND-URL/health` in browser
   - Should return: `{"status": "healthy"}`

---

## Part 2: Deploy Frontend to Vercel ▲

### Step 1: Update Frontend Environment

Update your frontend `.env.production` file:

```bash
cd /home/vanessa/Development/code/se-prep/phase-4/petbloom-frontend

# Edit .env.production with your actual values
```

File should look like:
```env
# Firebase (from Firebase Console)
VITE_FIREBASE_API_KEY=AIza...
VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-project.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=123456789
VITE_FIREBASE_APP_ID=1:123456789:web:abc123
VITE_FIREBASE_MEASUREMENT_ID=G-XXXXXXXXXX

# Backend API (use your Railway URL from Part 1, Step 5)
VITE_API_URL=https://petbloom-backend-production.up.railway.app/api/v1
```

### Step 2: Commit Frontend Changes

```bash
git add .env.production vercel.json
git commit -m "Add Vercel deployment config"
git push origin main
```

### Step 3: Deploy to Vercel

1. Go to https://vercel.com
2. Click **"Add New Project"**
3. Import your `petbloom-frontend` repository
4. Vercel auto-detects Vite settings
5. **Add Environment Variables:**
   - Click "Environment Variables"
   - Add all variables from `.env.production`
   - Click "Deploy"

### Step 4: Get Frontend URL

1. Wait for deployment to complete
2. Vercel will give you a URL (e.g., `https://petbloom-xyz.vercel.app`)
3. Test it in your browser

---

## Part 3: Final Configuration 🔗

### Update Backend CORS

1. Go back to Railway
2. Update `FRONTEND_URL` variable with your actual Vercel URL:
   ```
   FRONTEND_URL=https://petbloom-xyz.vercel.app
   ```
3. Railway will automatically redeploy

### Verify Everything Works

1. Open your Vercel app: `https://your-app.vercel.app`
2. Open browser console (F12)
3. Try to register/login
4. Check if API calls to Railway backend work
5. Test creating a user, browsing pets/products

---

## 🐛 Troubleshooting

### Frontend can't connect to backend
- **Check CORS**: Make sure `FRONTEND_URL` in Railway matches your Vercel URL exactly
- **Check API URL**: Verify `VITE_API_URL` in Vercel points to Railway backend
- **Check browser console**: Look for CORS errors or 404s

### Database connection issues
- **Check DATABASE_URL**: Should be set automatically by Railway PostgreSQL
- **Check logs**: Railway → Backend Service → Deployments → View Logs
- **Run migrations**: Should happen automatically via `prisma db push`

### Firebase authentication not working
- **Check credentials**: All Firebase variables must be set correctly
- **Private key format**: Must include quotes and `\n` newlines
- **Firebase console**: Add your Vercel domain to authorized domains

### Backend deployment fails
- **Check logs**: Railway → Deployments → View Logs  
- **Check Python version**: Should use Python 3.11
- **Check Prisma**: Make sure `prisma generate` runs during build

---

## 📝 Quick Reference

### Important URLs
- **Frontend (Vercel)**: `https://your-app.vercel.app`
- **Backend (Railway)**: `https://your-backend.railway.app`
- **API Docs**: `https://your-backend.railway.app/docs`
- **Health Check**: `https://your-backend.railway.app/health`

### Commands

```bash
# Local development - Backend
cd petbloom-backend
pip install -r requirements.txt
prisma generate
prisma db push
uvicorn app.main:app --reload

# Local development - Frontend  
cd petbloom-frontend
npm install
npm run dev

# Deploy Frontend
git push origin main  # Vercel auto-deploys

# Deploy Backend
git push origin main  # Railway auto-deploys
```

---

## ✅ Success Checklist

- [ ] Backend deployed to Railway
- [ ] PostgreSQL database created on Railway
- [ ] All environment variables set on Railway
- [ ] Backend health check returns success
- [ ] Frontend deployed to Vercel
- [ ] All environment variables set on Vercel
- [ ] Frontend loads without errors
- [ ] Can register/login users
- [ ] API calls from frontend reach backend
- [ ] CORS configured correctly
- [ ] Firebase authentication works

---

## 🎉 You're Done!

Your PetBloom app should now be fully deployed and functional!

**Share these URLs with your team:**
- Frontend: `https://your-app.vercel.app`
- Backend API: `https://your-backend.railway.app`
- API Docs: `https://your-backend.railway.app/docs`
