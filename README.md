# 💪 Workout Tracker

A comprehensive, production-ready workout tracking application built with Next.js, TypeScript, and Firebase.

## 🚀 Features

- **Authentication**: Email/password and Google sign-in with Firebase Auth
- **Workout Management**: Create and manage custom workout types with preloaded defaults
- **Session Logging**: Log detailed workout sessions with sets, reps, weight, RPE, and notes
- **Progress Tracking**: Visualize your progress with interactive charts and analytics
- **Multi-user Support**: Each user's data is isolated with Firestore security rules
- **Responsive Design**: Mobile-first, accessible UI with dark mode support
- **Type-Safe**: Strict TypeScript with Zod validation for runtime safety

## 🛠️ Tech Stack

- **Framework**: Next.js 14 (App Router)
- **Language**: TypeScript (strict mode)
- **Styling**: Tailwind CSS
- **Backend**: Firebase (Auth, Firestore, Storage)
- **Forms**: React Hook Form + Zod
- **Data Fetching**: SWR for client-side caching
- **Charts**: Recharts
- **UI Components**: Headless UI + Heroicons
- **Testing**: Vitest + Testing Library

## 📦 Installation

1. **Clone the repository**:

   ```bash
   git clone <your-repo-url>
   cd workout-tracker
   ```

2. **Install dependencies**:

   ```bash
   npm install
   ```

3. **Set up Firebase**:

   - Create a new Firebase project at [firebase.google.com](https://firebase.google.com)
   - Enable Authentication (Email/Password and Google providers)
   - Create a Firestore database (start in test mode, we'll add rules later)
   - Create a Storage bucket
   - Get your Firebase config from Project Settings

4. **Configure environment variables**:

   ```bash
   cp .env.local.example .env.local
   ```

   Edit `.env.local` and add your Firebase credentials:

   ```env
   NEXT_PUBLIC_FIREBASE_API_KEY=your_api_key
   NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=your_project_id.firebaseapp.com
   NEXT_PUBLIC_FIREBASE_PROJECT_ID=your_project_id
   NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=your_project_id.appspot.com
   NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
   NEXT_PUBLIC_FIREBASE_APP_ID=your_app_id
   NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID=your_measurement_id
   ```

5. **Deploy Firestore security rules**:

   ```bash
   # Install Firebase CLI if you haven't already
   npm install -g firebase-tools

   # Login to Firebase
   firebase login

   # Initialize Firebase in your project
   firebase init
   # Select Firestore and Storage
   # Choose your existing project
   # Use default file paths

   # Deploy rules
   firebase deploy --only firestore:rules,storage:rules
   ```

6. **Run the development server**:

   ```bash
   npm run dev
   ```

   Open [http://localhost:3000](http://localhost:3000) in your browser.

## 🧪 Testing

Run unit tests:

```bash
npm test
```

Run tests with UI:

```bash
npm run test:ui
```

Run type checking:

```bash
npm run type-check
```

## 🚢 Deployment

### Deploy to Vercel

1. **Install Vercel CLI** (optional):

   ```bash
   npm install -g vercel
   ```

2. **Deploy via Vercel Dashboard** (recommended):

   - Push your code to GitHub
   - Go to [vercel.com](https://vercel.com)
   - Import your repository
   - Add environment variables from `.env.local`
   - Deploy

3. **Deploy via CLI**:
   ```bash
   vercel --prod
   ```

### Post-Deployment Setup

1. **Update Firebase Auth Authorized Domains**:

   - Go to Firebase Console > Authentication > Settings > Authorized domains
   - Add your Vercel deployment URL (e.g., `your-app.vercel.app`)

2. **Test Authentication**:
   - Try signing up with email/password
   - Try Google sign-in
   - Verify that data is being saved to Firestore

## 📁 Project Structure

```
workout-tracker/
├── src/
│   ├── app/              # Next.js app directory
│   │   ├── (auth)/       # Auth pages (login, signup)
│   │   └── (dashboard)/  # Protected pages with sidebar layout
│   ├── components/       # React components
│   ├── contexts/         # React contexts (Auth, Settings)
│   ├── lib/              # Utilities and helpers
│   │   ├── firebase/     # Firebase configuration
│   │   ├── hooks/        # Custom React hooks
│   │   ├── schemas/      # Zod validation schemas
│   │   └── types/        # TypeScript type definitions
│   └── constants/        # App constants and defaults
├── firebase/             # Firebase configuration files
│   ├── firestore.rules   # Firestore security rules
│   └── storage.rules     # Storage security rules
├── tests/                # Test files
└── public/               # Static assets
```

## 🔒 Security

- **Authentication**: Firebase Auth handles user authentication securely
- **Authorization**: Firestore security rules ensure users can only access their own data
- **Data Validation**: Zod schemas validate all user input on the client
- **Type Safety**: Strict TypeScript prevents common runtime errors

## 🎯 Usage Guide

### First Time Setup

1. **Sign up** for an account using email/password or Google
2. **Load default workouts** from the My Workouts page (optional)
3. **Create custom workouts** or modify existing ones
4. **Log your first workout** using the Log Workout button

### Logging a Workout

1. Click "Log Workout" from the dashboard or navigation
2. Set the date and duration
3. Add exercises by selecting from your workout library
4. For each exercise, add sets with reps, weight, RPE, etc.
5. Add notes about how you felt or any observations
6. Save the workout

### Viewing Progress

1. Go to the Progress tab
2. Filter by exercise, time range, or granularity
3. View charts showing volume, frequency, and trends
4. Track your personal bests and improvements

## 🔧 Customization

### Adding New Workout Categories

Edit `src/lib/types/index.ts` and add to the `WorkoutCategory` enum:

```typescript
export enum WorkoutCategory {
  UPPER = "upper",
  LOWER = "lower",
  FULL_BODY = "full",
  CARDIO = "cardio",
  CORE = "core",
  YOUR_CATEGORY = "your_category", // Add here
}
```

### Modifying Default Workouts

Edit `src/lib/constants/defaultWorkouts.ts` to add, remove, or modify the preloaded workout templates.

## 🐛 Troubleshooting

### Firebase Authentication Errors

- Ensure authorized domains are configured in Firebase Console
- Check that environment variables are correctly set
- Verify Firebase Auth is enabled for your project

### Data Not Saving

- Check browser console for errors
- Verify Firestore security rules are deployed
- Ensure user is authenticated before making requests

### Build Errors

- Run `npm run type-check` to identify TypeScript errors
- Clear `.next` folder and rebuild: `rm -rf .next && npm run build`

## 📝 License

MIT License - feel free to use this project for personal or commercial purposes.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📧 Support

For issues or questions, please open an issue on GitHub.

---

Built with ❤️ using Next.js, TypeScript, and Firebase
