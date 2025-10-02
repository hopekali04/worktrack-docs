# 🏗️ Architecture Documentation

## System Overview

WorkTrack follows a modern, serverless architecture built on Next.js and Firebase. The application uses a client-side rendered approach with server-side routing, leveraging Firebase for authentication, real-time database operations, and security.

```
┌─────────────────────────────────────────────────────────────────┐
│                         Client Layer                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   Next.js    │  │   React      │  │  TypeScript  │          │
│  │  App Router  │  │  Components  │  │   + Zod      │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                              ↕
┌─────────────────────────────────────────────────────────────────┐
│                      Firebase Services                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │    Auth      │  │  Firestore   │  │   Storage    │          │
│  │              │  │  (NoSQL DB)  │  │  (Planned)   │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
```

## Architecture Principles

### 1. Client-Side First
- All rendering happens in the browser using React
- Next.js provides routing and build optimization
- API calls go directly to Firebase (no custom backend)

### 2. Type Safety
- Strict TypeScript throughout the codebase
- Zod schemas for runtime validation
- Type-safe Firebase operations with proper interfaces

### 3. Component Composition
- Small, reusable components with single responsibilities
- Headless UI for accessible primitives
- Clear separation between UI and business logic

### 4. Real-Time Data
- Firebase listeners for live updates
- SWR for client-side caching and revalidation
- Optimistic updates for better UX

### 5. Security by Default
- Firestore security rules enforce access control
- All data operations validated on the server
- User data completely isolated

## Project Structure

```
worktrack/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── (auth)/            # Authentication pages (login, signup)
│   │   │   ├── login/
│   │   │   └── signup/
│   │   ├── (dashboard)/       # Protected dashboard layout
│   │   │   ├── layout.tsx     # Sidebar navigation wrapper
│   │   │   ├── page.tsx       # Dashboard home
│   │   │   ├── workouts/      # Manage workout types
│   │   │   ├── log/           # Log and view workout sessions
│   │   │   ├── progress/      # Charts and analytics
│   │   │   └── settings/      # User settings
│   │   ├── layout.tsx         # Root layout with providers
│   │   ├── providers.tsx      # Context providers wrapper
│   │   └── globals.css        # Global styles and Tailwind
│   │
│   ├── components/            # Reusable React components
│   │   ├── auth/             # Login, signup forms
│   │   ├── layout/           # Sidebar, headers
│   │   ├── ui/               # Buttons, inputs, modals
│   │   ├── workouts/         # Workout type management
│   │   └── skeletons/        # Loading skeletons
│   │
│   ├── contexts/             # React Context providers
│   │   └── AuthContext.tsx   # User authentication state
│   │
│   ├── lib/                  # Utilities and helpers
│   │   ├── firebase/         # Firebase configuration
│   │   │   ├── config.ts     # Firebase app initialization
│   │   │   └── firestore.ts  # Firestore helper functions
│   │   ├── hooks/            # Custom React hooks
│   │   │   ├── useSessions.ts      # Workout session data
│   │   │   └── useWorkoutTypes.ts  # Workout type data
│   │   ├── schemas/          # Zod validation schemas
│   │   ├── types/            # TypeScript type definitions
│   │   │   └── index.ts      # All interfaces and enums
│   │   └── constants/        # App constants
│   │       └── defaultWorkouts.ts  # Pre-loaded exercises
│   │
│   └── constants/            # Deprecated, use lib/constants
│
├── firebase/                 # Firebase configuration files
│   └── firestore.indexes.json
│
├── firestore.rules          # Firestore security rules
├── firebase.json            # Firebase project config
│
├── public/                  # Static assets
│   ├── next.svg
│   ├── vercel.svg
│   └── ...
│
├── docs/                    # Documentation
│   ├── Index.md            # This overview
│   ├── Architecture.md     # System architecture
│   ├── API.md              # Data models and API
│   ├── SETUP.md            # Setup instructions
│   └── ideas.md            # Future features
│
├── next.config.ts          # Next.js configuration
├── tailwind.config.js      # Tailwind CSS configuration
├── tsconfig.json           # TypeScript configuration
└── package.json            # Dependencies and scripts
```

## Application Flow

### Authentication Flow

```
1. User visits app → Redirected to /login if not authenticated
                  ↓
2. User enters credentials → Firebase Auth validates
                  ↓
3. Firebase returns ID token → Stored in memory by Firebase SDK
                  ↓
4. AuthContext updates → User object available to all components
                  ↓
5. User redirected to dashboard → Protected routes now accessible
```

### Data Flow for Workout Logging

```
1. User navigates to /log/new
                  ↓
2. Form loads workout types from useWorkoutTypes hook
                  ↓
3. User fills in form → React Hook Form + Zod validate
                  ↓
4. On submit → Data sent to Firestore with userId
                  ↓
5. Firestore rules verify → User can only write their own data
                  ↓
6. Success → SWR cache updated → UI reflects changes
                  ↓
7. Real-time listener → Other tabs/devices get updates
```

## Core Components

### 1. App Router Structure

Next.js App Router provides file-based routing with layouts:

- **(auth)** group: Unauthenticated pages (login, signup)
- **(dashboard)** group: Protected pages with sidebar layout
- Route groups (parentheses) don't affect URL structure

### 2. Authentication Layer

**AuthContext** (`src/contexts/AuthContext.tsx`)
- Wraps entire app in `providers.tsx`
- Manages Firebase auth state
- Provides `user` and `loading` to all components
- Handles sign in, sign out, sign up operations

**Protected Routes**
- Dashboard layout checks auth state
- Redirects to `/login` if not authenticated
- Shows loading state during auth check

### 3. Data Management

**Custom Hooks**
- `useWorkoutTypes()`: Fetches and caches workout types for current user
- `useSessions()`: Fetches and caches workout sessions with filtering

**SWR Integration**
- Automatic caching and revalidation
- Request deduplication
- Optimistic updates
- Error retry with exponential backoff

**Firebase Listeners**
- Real-time data sync across devices
- Automatic updates when data changes
- Memory-efficient subscription management

### 4. UI Components

**Layout Components** (`src/components/layout/`)
- `Sidebar.tsx`: Main navigation for dashboard
- `Header.tsx`: Page headers with actions

**Form Components**
- Built with React Hook Form
- Zod validation with clear error messages
- Accessible form controls with labels

**Skeleton Loaders** (`src/components/skeletons/`)
- Shown during data loading
- Match actual content structure
- Animated with Tailwind

## Data Architecture

### Firestore Collections

```
users/{userId}
  - uid: string
  - email: string
  - displayName: string | null
  - photoURL: string | null
  - settings: UserSettings
  - createdAt: timestamp
  - updatedAt: timestamp

workoutTypes/{typeId}
  - userId: string (indexed)
  - name: string
  - category: WorkoutCategory
  - equipment: EquipmentType
  - muscleGroups: MuscleGroup[]
  - activityTags: ActivityTag[]
  - isDefault: boolean
  - createdAt: timestamp
  - updatedAt: timestamp

workoutSessions/{sessionId}
  - userId: string (indexed)
  - date: timestamp
  - duration: number
  - exercises: ExerciseEntry[]
  - notes: string
  - createdAt: timestamp
  - updatedAt: timestamp
```

### Data Relationships

- **One-to-Many**: User → WorkoutTypes (one user has many workout types)
- **One-to-Many**: User → WorkoutSessions (one user has many sessions)
- **Reference**: WorkoutSession.exercises[].workoutTypeId → WorkoutType.id

### Security Rules

All collections have user-based access control:

```javascript
// Users can only read/write their own data
allow read, write: if request.auth.uid == userId;

// On create, userId must match authenticated user
allow create: if request.auth.uid == request.resource.data.userId;
```

## State Management

### Global State (React Context)

**AuthContext**
- User authentication state
- Loading state
- Auth methods (signIn, signOut, signUp)

**Settings** (stored in Firestore)
- User preferences
- Unit system (metric/imperial)
- Dark mode preference
- Default rest time

### Local State

**Component State** (useState)
- Form inputs
- Modal visibility
- UI toggles

**SWR Cache** (automatic)
- Fetched data from Firestore
- Cached by unique key
- Revalidated on focus, reconnect

## Performance Optimizations

### 1. Code Splitting
- Next.js automatically splits routes
- Dynamic imports for heavy components
- Lazy loading for below-the-fold content

### 2. Image Optimization
- Next.js Image component
- Automatic WebP conversion
- Responsive image loading

### 3. Caching Strategy
- SWR caches all Firestore queries
- Stale-while-revalidate pattern
- Optimistic updates for instant feedback

### 4. Bundle Size
- Tree shaking removes unused code
- Minimal dependencies
- No heavy libraries

### 5. Database Optimization
- Composite indexes for common queries
- Efficient query structure (user-scoped)
- Pagination for large datasets

## Security Architecture

### Authentication
- Firebase Auth handles all auth operations
- JWT tokens for API authentication
- Automatic token refresh

### Authorization
- Firestore security rules enforce access control
- User can only access their own data
- Server-side validation on all writes

### Data Validation
- Client-side: Zod schemas validate before submission
- Server-side: Firestore rules validate structure
- Type safety: TypeScript catches errors at compile time

### XSS Prevention
- React escapes all dynamic content
- No `dangerouslySetInnerHTML` usage
- Content Security Policy headers (Vercel)

## Deployment Architecture

### Hosting
- **Vercel** (recommended): Automatic deployments from Git
- **Firebase Hosting** (alternative): CDN with custom domain support

### Environment Variables
- Stored in Vercel/Firebase
- Firebase config public (API keys protected by domain restrictions)
- No secrets in client code

### CI/CD Pipeline
1. Push to Git repository
2. Vercel detects changes
3. Runs `npm run build`
4. Deploys to preview URL (for PRs) or production (for main branch)
5. Automatic rollback on errors

### Monitoring
- Vercel Analytics for performance metrics
- Firebase Console for database usage
- Browser console for error tracking

## Scalability Considerations

### Current Scale
- Supports hundreds of concurrent users
- Firestore handles up to 1M reads/day on free tier
- No backend server to maintain

### Future Scaling
- Firestore auto-scales to millions of operations
- Add Cloud Functions for heavy computations
- Use Firestore caching for frequently accessed data
- Implement pagination for large result sets

## Testing Strategy

### Type Safety
- TypeScript strict mode catches errors at compile time
- Zod validates runtime data
- Firebase emulator for local testing

### Manual Testing
- Test authentication flows
- Test workout logging
- Test data visualization
- Cross-browser testing
- Mobile device testing

### Future: Automated Testing
- Vitest for unit tests
- Testing Library for component tests
- Playwright for E2E tests

## Accessibility Architecture

### Standards Compliance
- WCAG 2.1 Level AA compliance
- Semantic HTML elements
- ARIA attributes where needed

### Keyboard Navigation
- All interactive elements focusable
- Logical tab order
- Keyboard shortcuts for common actions

### Screen Readers
- Proper heading hierarchy
- Alt text for images
- ARIA labels for icon buttons
- Live regions for dynamic content

### Visual Accessibility
- Sufficient color contrast (4.5:1 minimum)
- Dark mode support
- Resizable text (no pixel units for font sizes)
- No information conveyed by color alone

## Technology Choices Rationale

### Why Next.js?
- File-based routing is intuitive
- Excellent TypeScript support
- Built-in optimization (images, fonts, code splitting)
- Great developer experience
- Easy deployment to Vercel

### Why Firebase?
- No backend code to write or maintain
- Real-time synchronization out of the box
- Built-in authentication
- Generous free tier
- Scales automatically

### Why TypeScript?
- Catch errors at compile time
- Better IDE support and autocomplete
- Easier refactoring
- Self-documenting code

### Why Tailwind CSS?
- Rapid development without context switching
- Small production bundle (unused styles purged)
- Consistent design system
- Mobile-first by default

### Why SWR?
- Simple API for data fetching
- Automatic caching and revalidation
- Built by Vercel (great Next.js integration)
- Optimistic updates built-in

## Future Architecture Enhancements

See [ideas.md](./ideas.md) for detailed feature proposals, including:

- **Progressive Web App**: Offline support with service workers
- **Cloud Functions**: Server-side processing for complex analytics
- **Redis Cache**: Faster data access for frequently queried data
- **GraphQL API**: Unified API layer for flexible data fetching
- **Microservices**: Separate services for recommendations, notifications
- **Real-time Collaboration**: Share workouts with friends or trainers
- **Mobile App**: React Native app sharing core business logic

---

This architecture provides a solid foundation for a production-ready workout tracking application while remaining flexible for future enhancements.
