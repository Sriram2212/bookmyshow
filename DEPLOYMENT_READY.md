# 🚀 Deployment Ready Guide

Your BookMyShow application is now ready for deployment! This guide covers deployment to various platforms.

## 📋 Pre-Deployment Checklist

### ✅ Backend (Server)
- [x] `.env.example` file created with all required variables
- [x] `Procfile` for Heroku/Railway deployment
- [x] `vercel.json` for Vercel deployment
- [x] `render.yaml` for Render deployment
- [x] Production dependencies configured
- [x] MongoDB connection ready
- [x] CORS configuration in place

### ✅ Frontend (Client)
- [x] `.env.example` file created
- [x] `vercel.json` for Vercel deployment
- [x] `netlify.toml` for Netlify deployment
- [x] Build script configured
- [x] React Router redirects configured

## 🔧 Environment Variables Setup

### Backend Environment Variables

Create a `.env` file in the `server` folder (or set these in your hosting platform):

```env
# MongoDB Connection (REQUIRED)
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/bookmyshow

# JWT Secret (REQUIRED - Generate a strong secret!)
JWT_SECRET=your-super-secret-jwt-key-minimum-32-characters-long

# Server Configuration
PORT=5000
NODE_ENV=production

# Client URL (REQUIRED - Set to your frontend domain)
CLIENT_URL=https://your-frontend-domain.com

# Redis (OPTIONAL - App works without it)
# REDIS_URL=redis://default:password@redis-host:port
```

**Generate a secure JWT secret:**
```bash
node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"
```

### Frontend Environment Variables

Create a `.env` file in the `client` folder (or set in hosting platform):

```env
# Backend API URL (REQUIRED)
REACT_APP_API_URL=https://your-backend-domain.com/api
```

## 🌐 Deployment Options

### Option 1: Deploy Backend to Render

**Render** is recommended for the backend (free tier available).

1. **Create MongoDB Atlas Database** (if not already done)
   - Go to [mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
   - Create a free cluster
   - Get connection string
   - Whitelist all IPs (0.0.0.0/0) for production

2. **Deploy to Render**
   - Go to [render.com](https://render.com)
   - Click "New +" → "Web Service"
   - Connect your GitHub repository
   - Select the `server` folder
   - Render will auto-detect the `render.yaml` configuration
   - Set environment variables:
     - `MONGODB_URI`: Your MongoDB Atlas connection string
     - `JWT_SECRET`: Your generated secret
     - `CLIENT_URL`: Your frontend URL (set after deploying frontend)
   - Click "Create Web Service"
   - Copy the deployed URL (e.g., `https://bookmyshow-api.onrender.com`)

### Option 2: Deploy Backend to Railway

1. Go to [railway.app](https://railway.app)
2. Click "New Project" → "Deploy from GitHub repo"
3. Select your repository
4. Set root directory to `server`
5. Add environment variables (same as above)
6. Deploy!

### Option 3: Deploy Backend to Heroku

```bash
cd server
heroku create bookmyshow-backend
heroku config:set MONGODB_URI="your-mongodb-uri"
heroku config:set JWT_SECRET="your-jwt-secret"
heroku config:set CLIENT_URL="your-frontend-url"
heroku config:set NODE_ENV=production
git push heroku main
```

### Option 4: Deploy Backend to Vercel

```bash
cd server
vercel
# Follow prompts and set environment variables in Vercel dashboard
```

---

### Deploy Frontend to Vercel

**Vercel** is recommended for React apps (free tier available).

1. **Update Environment Variable**
   - Create `.env` in `client` folder:
   ```env
   REACT_APP_API_URL=https://your-backend-url.com/api
   ```

2. **Deploy to Vercel**
   ```bash
   cd client
   npm install -g vercel
   vercel
   ```
   
   Or use Vercel Dashboard:
   - Go to [vercel.com](https://vercel.com)
   - Click "New Project"
   - Import your GitHub repository
   - Set root directory to `client`
   - Add environment variable: `REACT_APP_API_URL`
   - Click "Deploy"

3. **Copy Frontend URL**
   - After deployment, copy the URL (e.g., `https://bookmyshow.vercel.app`)
   - Update backend's `CLIENT_URL` environment variable with this URL

### Deploy Frontend to Netlify

1. **Deploy via Netlify Dashboard**
   - Go to [netlify.com](https://netlify.com)
   - Click "Add new site" → "Import an existing project"
   - Connect GitHub repository
   - Set base directory to `client`
   - Build command: `npm run build`
   - Publish directory: `build`
   - Add environment variable: `REACT_APP_API_URL`
   - Click "Deploy"

2. **Or use Netlify CLI**
   ```bash
   cd client
   npm install -g netlify-cli
   netlify deploy --prod
   ```

---

## 🔄 Post-Deployment Steps

### 1. Update CORS Configuration

After deploying frontend, update backend's `CLIENT_URL`:

**On Render:**
- Go to your service → Environment
- Update `CLIENT_URL` to your frontend URL
- Save changes (service will redeploy)

**On Vercel:**
- Go to your project → Settings → Environment Variables
- Update `CLIENT_URL`
- Redeploy

### 2. Test the Deployment

1. **Test Backend Health**
   ```bash
   curl https://your-backend-url.com/health
   # Should return: {"status":"OK","message":"Server is running"}
   ```

2. **Test Frontend**
   - Visit your frontend URL
   - Try signing up/logging in
   - Test booking flow

### 3. Seed Production Database (Optional)

```bash
# SSH into your backend server or run locally with production MongoDB URI
cd server
node src/scripts/seedData.js
```

Or use MongoDB Compass to import sample data.

## 🗄️ Database Setup (MongoDB Atlas)

1. **Create Free Cluster**
   - Go to [mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
   - Sign up and create a free M0 cluster
   - Choose a cloud provider and region

2. **Create Database User**
   - Go to "Database Access"
   - Add new database user
   - Save username and password

3. **Configure Network Access**
   - Go to "Network Access"
   - Add IP Address: `0.0.0.0/0` (allow from anywhere)
   - Or add specific IPs of your hosting platform

4. **Get Connection String**
   - Click "Connect" on your cluster
   - Choose "Connect your application"
   - Copy the connection string
   - Replace `<password>` with your database user password
   - Replace `<dbname>` with `bookmyshow`

Example:
```
mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/bookmyshow?retryWrites=true&w=majority
```

## 🔒 Security Best Practices

### Before Going Live:

1. **Generate Strong JWT Secret**
   ```bash
   node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"
   ```

2. **Use Environment Variables**
   - Never commit `.env` files
   - Use platform's environment variable settings

3. **Enable HTTPS**
   - Most platforms (Vercel, Netlify, Render) provide free SSL
   - Ensure both frontend and backend use HTTPS

4. **Restrict CORS**
   - Set `CLIENT_URL` to your exact frontend domain
   - Don't use wildcards in production

5. **MongoDB Security**
   - Use strong database passwords
   - Enable IP whitelisting if possible
   - Use MongoDB Atlas for managed security

## 📊 Monitoring & Logs

### Backend Logs

**Render:**
- Dashboard → Your Service → Logs

**Railway:**
- Project → Deployments → View Logs

**Vercel:**
- Project → Deployments → Click deployment → Runtime Logs

### Frontend Logs

**Vercel/Netlify:**
- Check build logs in deployment details
- Use browser console for runtime errors

## 🐛 Troubleshooting

### Common Issues

**1. CORS Error**
```
Access to fetch at 'backend-url' from origin 'frontend-url' has been blocked by CORS
```
**Solution:** Update `CLIENT_URL` in backend environment variables to match your frontend URL.

**2. MongoDB Connection Failed**
```
MongoNetworkError: failed to connect
```
**Solution:** 
- Check MongoDB Atlas IP whitelist (0.0.0.0/0)
- Verify connection string is correct
- Ensure password doesn't contain special characters (URL encode if needed)

**3. Environment Variables Not Working**
**Solution:**
- Restart/redeploy after changing environment variables
- For React, variables must start with `REACT_APP_`
- Rebuild frontend after changing environment variables

**4. 404 on Frontend Routes**
**Solution:** 
- Ensure `vercel.json` or `netlify.toml` is configured for SPA redirects
- Check that rewrites are set to redirect all routes to `index.html`

**5. API Calls Failing**
**Solution:**
- Check `REACT_APP_API_URL` is set correctly
- Ensure backend is deployed and running
- Check browser console for exact error

## 🎯 Quick Deployment Commands

### Full Deployment (Recommended Flow)

```bash
# 1. Deploy Backend to Render (via dashboard)
# 2. Copy backend URL

# 3. Update frontend .env
cd client
echo "REACT_APP_API_URL=https://your-backend-url.com/api" > .env

# 4. Deploy frontend to Vercel
vercel --prod

# 5. Copy frontend URL and update backend CLIENT_URL in Render dashboard
```

### Alternative: Deploy Both to Vercel

```bash
# Backend
cd server
vercel --prod

# Frontend
cd ../client
vercel --prod
```

## 📱 Testing Production Deployment

1. **Create Test Account**
   - Visit your frontend URL
   - Sign up with test credentials

2. **Test Full Booking Flow**
   - Browse movies
   - Select a show
   - Choose seats
   - Complete booking
   - Check booking history

3. **Verify Database**
   - Use MongoDB Compass
   - Connect to your Atlas cluster
   - Check `users`, `bookings`, `movies` collections

## 🎉 Deployment Complete!

Your BookMyShow application is now live! 

**Share your URLs:**
- Frontend: `https://your-app.vercel.app`
- Backend API: `https://your-api.onrender.com/api`

## 📞 Support & Next Steps

### Recommended Enhancements:
1. Set up custom domain names
2. Enable Redis for caching (Redis Cloud free tier)
3. Add monitoring (Sentry, LogRocket)
4. Set up CI/CD pipelines
5. Add rate limiting
6. Implement email notifications
7. Add payment gateway integration

### Platform-Specific Docs:
- [Render Docs](https://render.com/docs)
- [Vercel Docs](https://vercel.com/docs)
- [Netlify Docs](https://docs.netlify.com)
- [Railway Docs](https://docs.railway.app)
- [MongoDB Atlas Docs](https://docs.atlas.mongodb.com)

---

**Need help?** Check the deployment logs and error messages. Most issues are related to environment variables or CORS configuration.
