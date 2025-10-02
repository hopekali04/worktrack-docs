# ⚙️ Setup Guide

## Overview

This guide will walk you through setting up WorkTrack for local development and deploying it to production. Follow these steps in order for a smooth setup experience.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** 18.0 or higher ([download](https://nodejs.org/))
- **npm** 9.0 or higher (comes with Node.js)
- **Git** ([download](https://git-scm.com/))
- A **Firebase account** (free tier is sufficient)
- A **Vercel account** (optional, for deployment)

Check your versions:
```bash
node --version  # Should be v18.0.0 or higher
npm --version   # Should be 9.0.0 or higher
git --version   # Any recent version
```

## Local Development Setup

### 1. Clone the Repository

```bash
git clone https://github.com/hopekali04/worktrack.git
cd worktrack
```

### 2. Install Dependencies

```bash
npm install
```

This will install all required dependencies listed in `package.json`, including:
- Next.js, React, TypeScript
- Firebase SDK
- Tailwind CSS
- Form libraries (React Hook Form, Zod)
- UI libraries (Headless UI, Heroicons)
- And more...

### 3. Firebase Project Setup

#### Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click **"Add project"**
3. Enter project name (e.g., "worktrack-dev")
4. Disable Google Analytics (optional for development)
5. Click **"Create project"**

#### Enable Authentication

1. In Firebase Console, go to **Build → Authentication**
2. Click **"Get started"**
3. Enable **Email/Password** provider:
   - Click on "Email/Password"
   - Toggle "Enable"
   - Click "Save"
4. Enable **Google** provider:
   - Click on "Google"
   - Toggle "Enable"
   - Enter support email
   - Click "Save"

#### Create Firestore Database

1. Go to **Build → Firestore Database**
2. Click **"Create database"**
3. Choose **"Start in test mode"** (we'll add security rules later)
4. Select a location close to your users
5. Click **"Enable"**

#### Create Storage Bucket (Optional)

1. Go to **Build → Storage**
2. Click **"Get started"**
3. Start in **test mode**
4. Use default location
5. Click **"Done"**

#### Get Firebase Configuration

1. Go to **Project Settings** (gear icon)
2. Scroll down to **"Your apps"**
3. Click the **web icon** (`</>`) to add a web app
4. Enter app nickname (e.g., "WorkTrack Web")
5. Don't check "Firebase Hosting" (we'll use Vercel)
6. Click **"Register app"**
7. Copy the configuration object - you'll need these values:

```javascript
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project-id",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123:web:abc123",
  measurementId: "G-ABC123" // Optional
};
```

### 4. Configure Environment Variables

Create a `.env.local` file in the root directory:

```bash
cp .env.example .env.local
```

Edit `.env.local` and add your Firebase configuration:

```env
# Firebase Configuration
NEXT_PUBLIC_FIREBASE_API_KEY=your_api_key_here
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=your-project-id.firebaseapp.com
NEXT_PUBLIC_FIREBASE_PROJECT_ID=your-project-id
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=your-project-id.appspot.com
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
NEXT_PUBLIC_FIREBASE_APP_ID=your_app_id
NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID=your_measurement_id
```

**Important Notes**:
- All variables start with `NEXT_PUBLIC_` because they're used in the browser
- Do NOT commit `.env.local` to Git (it's in `.gitignore`)
- Firebase API keys are safe to expose publicly (they're restricted by domain and security rules)

### 5. Deploy Firebase Security Rules

Install Firebase CLI globally:

```bash
npm install -g firebase-tools
```

Login to Firebase:

```bash
firebase login
```

Initialize Firebase in your project:

```bash
firebase init
```

Select the following options:
- **Which features?** → Firestore, Storage (use spacebar to select)
- **Select a default Firebase project** → Choose your project
- **Firestore rules file** → `firestore.rules` (default)
- **Firestore indexes file** → `firebase/firestore.indexes.json`
- **Storage rules file** → `storage.rules` (default)

Deploy the security rules:

```bash
firebase deploy --only firestore:rules,storage:rules
```

### 6. Run the Development Server

Start the development server:

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

You should see the login page. If you see any errors:
- Check the browser console for error messages
- Verify your `.env.local` file has correct values
- Ensure Firebase services are enabled in the console

### 7. Test the Application

1. **Sign up for an account**:
   - Click "Sign up" 
   - Enter email and password
   - Submit the form

2. **Verify authentication**:
   - Check that you're redirected to the dashboard
   - Check Firebase Console → Authentication → Users to see your account

3. **Test workout type creation**:
   - Go to "My Workouts"
   - Click "Load Default Workouts" or "Add Workout"
   - Verify data appears in Firebase Console → Firestore Database

4. **Log a workout**:
   - Click "Log Workout"
   - Fill in the form
   - Verify the session appears in the dashboard

## Firebase Security Rules Explained

### Firestore Rules (`firestore.rules`)

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can read and write their own profile
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Users can read and write their own workout types
    match /workoutTypes/{typeId} {
      allow read, write: if request.auth != null && request.auth.uid == resource.data.userId;
      allow create: if request.auth != null && request.auth.uid == request.resource.data.userId;
    }
    
    // Users can read and write their own workout sessions
    match /workoutSessions/{sessionId} {
      allow read, write: if request.auth != null && request.auth.uid == resource.data.userId;
      allow create: if request.auth != null && request.auth.uid == request.resource.data.userId;
    }
  }
}
```

**Key Points**:
- Users must be authenticated (`request.auth != null`)
- Users can only access documents where `userId` matches their `uid`
- On create, the `userId` field must match the authenticated user
- This ensures complete data isolation between users

### Storage Rules (Future)

For user uploads like profile pictures:

```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /users/{userId}/{allPaths=**} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

## Development Workflow

### Starting Development

```bash
# Start dev server with Turbopack (faster)
npm run dev

# Or with legacy Webpack
npm run dev -- --no-turbopack
```

### Code Quality

```bash
# Run ESLint
npm run lint

# Type checking
npx tsc --noEmit
```

### Building for Production

```bash
# Create production build
npm run build

# Test production build locally
npm run start
```

### Common Development Tasks

**Clear Next.js cache**:
```bash
rm -rf .next
npm run dev
```

**Update dependencies**:
```bash
npm update
npm outdated  # Check for newer versions
```

**Add new dependency**:
```bash
npm install package-name
npm install -D package-name  # For dev dependencies
```

## Production Deployment

### Deploy to Vercel (Recommended)

Vercel is the easiest way to deploy Next.js applications.

#### Option 1: Deploy via Vercel Dashboard

1. **Push code to GitHub**:
   ```bash
   git add .
   git commit -m "Initial commit"
   git push origin main
   ```

2. **Import to Vercel**:
   - Go to [vercel.com](https://vercel.com)
   - Click "Add New Project"
   - Import your GitHub repository
   - Vercel auto-detects Next.js configuration

3. **Add environment variables**:
   - In project settings → Environment Variables
   - Add all variables from `.env.local`
   - Apply to Production, Preview, and Development

4. **Deploy**:
   - Click "Deploy"
   - Wait for build to complete (~2-3 minutes)
   - Access your app at `your-app.vercel.app`

#### Option 2: Deploy via Vercel CLI

1. **Install Vercel CLI**:
   ```bash
   npm install -g vercel
   ```

2. **Login**:
   ```bash
   vercel login
   ```

3. **Deploy**:
   ```bash
   vercel --prod
   ```

4. **Follow prompts**:
   - Link to existing project or create new
   - Set environment variables when prompted

### Post-Deployment Configuration

#### Update Firebase Authorized Domains

1. Go to Firebase Console → Authentication → Settings
2. Scroll to **Authorized domains**
3. Click **"Add domain"**
4. Add your Vercel domain (e.g., `your-app.vercel.app`)
5. Click **"Add"**

Without this, Google Sign-In won't work on your deployed app.

#### Test Production Deployment

1. **Test authentication**:
   - Sign up with a new account
   - Try Google Sign-In
   - Verify you can log in

2. **Test data operations**:
   - Create workout types
   - Log workout sessions
   - View progress charts

3. **Test on multiple devices**:
   - Mobile phone
   - Tablet
   - Desktop browsers (Chrome, Firefox, Safari)

### Deploy to Firebase Hosting (Alternative)

If you prefer Firebase Hosting over Vercel:

1. **Build the app**:
   ```bash
   npm run build
   ```

2. **Initialize Firebase Hosting**:
   ```bash
   firebase init hosting
   ```
   
   Select:
   - Public directory: `out`
   - Configure as SPA: `Yes`
   - Overwrite index.html: `No`

3. **Export static site** (requires Next.js config changes):
   
   Edit `next.config.ts`:
   ```typescript
   const nextConfig = {
     output: 'export',
   };
   ```

4. **Build and deploy**:
   ```bash
   npm run build
   firebase deploy --only hosting
   ```

**Note**: Firebase Hosting requires static export, which disables some Next.js features. Vercel is recommended for full feature support.

## Troubleshooting

### Firebase Configuration Issues

**Error**: `Firebase: Error (auth/invalid-api-key)`

**Solution**: 
- Check that `NEXT_PUBLIC_FIREBASE_API_KEY` in `.env.local` is correct
- Verify you copied the exact value from Firebase Console
- Restart dev server after changing `.env.local`

### Authentication Issues

**Error**: `auth/unauthorized-domain`

**Solution**:
- Go to Firebase Console → Authentication → Settings → Authorized domains
- Add `localhost` for local development
- Add your Vercel domain for production

**Error**: Google Sign-In button doesn't appear

**Solution**:
- Verify Google provider is enabled in Firebase Console
- Check browser console for errors
- Ensure you entered a support email in Google provider settings

### Firestore Permission Issues

**Error**: `FirebaseError: Missing or insufficient permissions`

**Solution**:
- Deploy Firestore rules: `firebase deploy --only firestore:rules`
- Verify user is authenticated before accessing data
- Check that `userId` field matches authenticated user's UID

### Build Errors

**Error**: Type errors during build

**Solution**:
```bash
# Check for type errors
npx tsc --noEmit

# Fix errors in code
# Then rebuild
npm run build
```

**Error**: `ENOENT: no such file or directory`

**Solution**:
```bash
# Clear cache and reinstall
rm -rf .next node_modules
npm install
npm run build
```

### Port Already in Use

**Error**: `Port 3000 is already in use`

**Solution**:
```bash
# Find process using port 3000
lsof -ti:3000

# Kill the process (macOS/Linux)
kill -9 $(lsof -ti:3000)

# Or use a different port
npm run dev -- -p 3001
```

### Environment Variables Not Loading

**Solution**:
- Ensure file is named exactly `.env.local` (not `.env` or `.env.development`)
- All variables for browser must start with `NEXT_PUBLIC_`
- Restart dev server after changing environment variables
- For Vercel, ensure variables are set in dashboard

## Database Maintenance

### Backup Firestore Data

Use Firebase CLI to export data:

```bash
# Export entire database
firebase firestore:export gs://your-bucket-name/backups/$(date +%Y%m%d)

# Export specific collection
firebase firestore:export --collection-ids=workoutSessions gs://your-bucket-name/backups
```

### Restore Firestore Data

```bash
firebase firestore:import gs://your-bucket-name/backups/20240101
```

### Monitor Usage

Check Firebase Console → Usage and billing to monitor:
- Firestore reads/writes
- Authentication sign-ins
- Storage usage
- Function invocations (if using Cloud Functions)

Free tier limits:
- **Firestore**: 50K reads, 20K writes, 20K deletes per day
- **Authentication**: Unlimited
- **Storage**: 5GB storage, 1GB/day downloads

## CI/CD Setup (Optional)

### GitHub Actions for Vercel

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to Vercel

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          
      - name: Install dependencies
        run: npm ci
        
      - name: Run linter
        run: npm run lint
        
      - name: Type check
        run: npx tsc --noEmit
        
      - name: Build
        run: npm run build
        env:
          NEXT_PUBLIC_FIREBASE_API_KEY: ${{ secrets.FIREBASE_API_KEY }}
          # ... other environment variables
```

### Firebase Hosting with GitHub Actions

```yaml
name: Deploy to Firebase Hosting

on:
  push:
    branches: [main]

jobs:
  build_and_deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          
      - name: Install dependencies
        run: npm ci
        
      - name: Build
        run: npm run build
        
      - name: Deploy to Firebase
        uses: FirebaseExtended/action-hosting-deploy@v0
        with:
          repoToken: '${{ secrets.GITHUB_TOKEN }}'
          firebaseServiceAccount: '${{ secrets.FIREBASE_SERVICE_ACCOUNT }}'
          channelId: live
```

## Performance Optimization

### Analyze Bundle Size

```bash
# Install bundle analyzer
npm install -D @next/bundle-analyzer

# Add to next.config.ts
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer(nextConfig);

# Run analysis
ANALYZE=true npm run build
```

### Lighthouse Testing

```bash
# Install Lighthouse
npm install -g lighthouse

# Run Lighthouse on local build
npm run build
npm run start
lighthouse http://localhost:3000 --view
```

Target scores:
- Performance: 90+
- Accessibility: 95+
- Best Practices: 95+
- SEO: 90+

## Next Steps

After completing setup:

1. Read [Architecture.md](./Architecture.md) to understand system design
2. Review [API.md](./API.md) for data models and hooks
3. Explore [ideas.md](./ideas.md) for feature ideas
4. Start building!

## Getting Help

- **Firebase Documentation**: https://firebase.google.com/docs
- **Next.js Documentation**: https://nextjs.org/docs
- **Tailwind CSS**: https://tailwindcss.com/docs
- **GitHub Issues**: Report bugs or request features

---

Happy coding! 🚀
