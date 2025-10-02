# 🔌 API Documentation

## Overview

WorkTrack uses Firebase Firestore as its database with a client-side SDK. There are no custom REST or GraphQL endpoints. All data operations happen through Firebase's real-time database API, with React hooks providing a clean abstraction layer.

## Data Models

### Core Enums

#### WorkoutCategory
```typescript
enum WorkoutCategory {
  UPPER = "upper",       // Upper body exercises
  LOWER = "lower",       // Lower body exercises
  FULL_BODY = "full",    // Full body compound movements
  CARDIO = "cardio",     // Cardiovascular exercises
  CORE = "core",         // Core and abs exercises
}
```

#### EquipmentType
```typescript
enum EquipmentType {
  BODYWEIGHT = "bodyweight",           // Push-ups, pull-ups, etc.
  BARBELL = "barbell",                 // Barbell exercises
  DUMBBELL = "dumbbell",               // Dumbbell exercises
  MACHINE = "machine",                 // Weight machines
  CABLE = "cable",                     // Cable machines
  CARDIO_EQUIPMENT = "cardio_equipment", // Treadmill, bike, etc.
  OTHER = "other",                     // Resistance bands, kettlebells
}
```

#### MuscleGroup
```typescript
enum MuscleGroup {
  CHEST = "chest",
  BACK = "back",
  SHOULDERS = "shoulders",
  BICEPS = "biceps",
  TRICEPS = "triceps",
  FOREARMS = "forearms",
  ABS = "abs",
  OBLIQUES = "obliques",
  QUADS = "quads",
  HAMSTRINGS = "hamstrings",
  GLUTES = "glutes",
  CALVES = "calves",
  CARDIO = "cardio",
}
```

#### ActivityTag
```typescript
enum ActivityTag {
  STRENGTH = "strength",     // Traditional strength training
  RUNNING = "running",       // Running activities
  SPRINTS = "sprints",       // Sprint training
  CYCLING = "cycling",       // Cycling activities
  SWIMMING = "swimming",     // Swimming activities
  HIIT = "hiit",            // High-intensity interval training
  STRETCHING = "stretching", // Flexibility and mobility
}
```

#### UnitSystem
```typescript
enum UnitSystem {
  METRIC = "metric",     // kg, km
  IMPERIAL = "imperial", // lbs, miles
}
```

### User Profile

**Collection**: `users/{userId}`

```typescript
interface UserProfile {
  uid: string;                    // Firebase Auth UID
  email: string;                  // User email address
  displayName: string | null;     // User's display name
  photoURL: string | null;        // Profile photo URL
  createdAt: Timestamp;           // Account creation date
  updatedAt: Timestamp;           // Last profile update
  settings: UserSettings;         // User preferences
  onboardingCompleted: boolean;   // Whether user completed setup
}

interface UserSettings {
  unitSystem: UnitSystem;         // Metric or imperial
  darkMode: boolean;              // Dark mode preference
  defaultRestTime: number;        // Default rest time in seconds
  notificationsEnabled: boolean;  // Push notification preference
}
```

**Security Rules**:
```javascript
// Users can only read/write their own profile
allow read, write: if request.auth.uid == userId;
```

### Workout Type (Exercise Definition)

**Collection**: `workoutTypes/{typeId}`

```typescript
interface WorkoutType {
  id: string;                     // Firestore document ID
  userId: string;                 // Owner of this workout type
  name: string;                   // Exercise name (e.g., "Bench Press")
  category: WorkoutCategory;      // Exercise category
  equipment: EquipmentType;       // Required equipment
  muscleGroups: MuscleGroup[];    // Target muscle groups
  activityTags: ActivityTag[];    // Activity classifications
  isDefault: boolean;             // Whether this is a system default
  createdAt: Timestamp;           // Creation timestamp
  updatedAt: Timestamp;           // Last update timestamp
}
```

**Form Input Type** (for creating new workout types):
```typescript
interface WorkoutTypeInput {
  name: string;
  category: WorkoutCategory;
  equipment: EquipmentType;
  muscleGroups: MuscleGroup[];
  activityTags: ActivityTag[];
}
```

**Security Rules**:
```javascript
// Users can read/write their own workout types
allow read, write: if request.auth.uid == resource.data.userId;
allow create: if request.auth.uid == request.resource.data.userId;
```

**Indexes**:
- `userId` (ascending) - for querying user's workout types
- `userId, category` (composite) - for filtering by category
- `userId, muscleGroups` (array-contains) - for filtering by muscle group

### Workout Session

**Collection**: `workoutSessions/{sessionId}`

```typescript
interface WorkoutSession {
  id: string;                     // Firestore document ID
  userId: string;                 // Owner of this session
  date: Timestamp;                // When workout occurred
  duration: number;               // Duration in minutes
  exercises: ExerciseEntry[];     // List of exercises performed
  notes?: string;                 // Optional session notes
  createdAt: Timestamp;           // When logged in system
  updatedAt: Timestamp;           // Last update
}

interface ExerciseEntry {
  workoutTypeId: string;          // Reference to WorkoutType
  workoutTypeName: string;        // Cached name for display
  sets: SetEntry[];               // List of sets performed
  notes?: string;                 // Exercise-specific notes
}

interface SetEntry {
  reps?: number;                  // Number of repetitions
  weight?: number;                // Weight used (kg or lbs)
  rpe?: number;                   // Rate of Perceived Exertion (1-10)
  distance?: number;              // Distance (km or miles) for cardio
  time?: number;                  // Time (seconds) for cardio/timed exercises
}
```

**Form Input Types** (for creating new sessions):
```typescript
interface WorkoutSessionInput {
  date: Date;
  duration: number;
  notes?: string;
  exercises: ExerciseEntryInput[];
}

interface ExerciseEntryInput {
  workoutTypeId: string;
  sets: SetEntryInput[];
  notes?: string;
}

interface SetEntryInput {
  reps?: number;
  weight?: number;
  rpe?: number;
  distance?: number;
  time?: number;
}
```

**Security Rules**:
```javascript
// Users can read/write their own workout sessions
allow read, write: if request.auth.uid == resource.data.userId;
allow create: if request.auth.uid == request.resource.data.userId;
```

**Indexes**:
- `userId, date` (descending) - for chronological listing
- `userId, createdAt` (descending) - for recent sessions

### Personal Record (Future)

**Collection**: `personalRecords/{recordId}`

```typescript
interface PersonalRecord {
  userId: string;                 // Owner of this record
  workoutTypeId: string;          // Which exercise
  workoutTypeName: string;        // Cached name
  metric: "weight" | "reps" | "volume" | "distance" | "time";
  value: number;                  // The record value
  sessionId: string;              // Which session achieved this
  date: Timestamp;                // When achieved
  createdAt: Timestamp;           // When recorded in system
}
```

### Goal (Future)

**Collection**: `goals/{goalId}`

```typescript
interface Goal {
  id: string;
  userId: string;
  workoutTypeId?: string;         // Optional: specific exercise goal
  targetMetric: "weight" | "reps" | "volume" | "distance" | "time" | "frequency";
  targetValue: number;            // Goal target
  currentValue: number;           // Current progress
  deadline?: Timestamp;           // Optional deadline
  completed: boolean;             // Whether goal is achieved
  createdAt: Timestamp;
  updatedAt: Timestamp;
}
```

## Custom Hooks

### useWorkoutTypes

Fetches and manages workout types for the current user.

```typescript
import { useWorkoutTypes } from '@/lib/hooks/useWorkoutTypes';

function MyComponent() {
  const { 
    data: workoutTypes,     // WorkoutType[]
    error,                  // Error | undefined
    isLoading,             // boolean
    mutate                 // Function to refresh data
  } = useWorkoutTypes();
  
  // Use workoutTypes...
}
```

**Features**:
- Automatically scoped to current user
- Real-time updates via Firebase listener
- Cached with SWR
- Error handling built-in

**Implementation**:
```typescript
export function useWorkoutTypes() {
  const { user } = useAuth();
  
  const { data, error, isLoading, mutate } = useSWR(
    user ? ['workoutTypes', user.uid] : null,
    async () => {
      const q = query(
        collection(db, 'workoutTypes'),
        where('userId', '==', user.uid),
        orderBy('name')
      );
      const snapshot = await getDocs(q);
      return snapshot.docs.map(doc => ({
        id: doc.id,
        ...doc.data()
      })) as WorkoutType[];
    }
  );
  
  return { data, error, isLoading, mutate };
}
```

### useSessions

Fetches workout sessions with optional filtering.

```typescript
import { useSessions } from '@/lib/hooks/useSessions';

function MyComponent() {
  const { 
    data: sessions,         // WorkoutSession[]
    error,                  // Error | undefined
    isLoading,             // boolean
    mutate                 // Function to refresh data
  } = useSessions({
    startDate: new Date('2024-01-01'),
    endDate: new Date('2024-12-31'),
    workoutTypeId: 'bench-press-id', // optional filter
  });
  
  // Use sessions...
}
```

**Parameters**:
```typescript
interface UseSessionsParams {
  startDate?: Date;              // Filter by start date
  endDate?: Date;                // Filter by end date
  workoutTypeId?: string;        // Filter by specific exercise
  limit?: number;                // Limit number of results
}
```

**Features**:
- Real-time updates
- Date range filtering
- Exercise filtering
- Pagination support
- Automatic caching

## Firebase Operations

### Creating Data

**Create Workout Type**:
```typescript
import { addDoc, collection, serverTimestamp } from 'firebase/firestore';
import { db } from '@/lib/firebase/config';

async function createWorkoutType(data: WorkoutTypeInput) {
  const { user } = useAuth();
  
  const docRef = await addDoc(collection(db, 'workoutTypes'), {
    ...data,
    userId: user.uid,
    isDefault: false,
    createdAt: serverTimestamp(),
    updatedAt: serverTimestamp(),
  });
  
  return docRef.id;
}
```

**Create Workout Session**:
```typescript
async function createWorkoutSession(data: WorkoutSessionInput) {
  const { user } = useAuth();
  
  const docRef = await addDoc(collection(db, 'workoutSessions'), {
    ...data,
    userId: user.uid,
    date: Timestamp.fromDate(data.date),
    createdAt: serverTimestamp(),
    updatedAt: serverTimestamp(),
  });
  
  return docRef.id;
}
```

### Reading Data

**Get Single Document**:
```typescript
import { doc, getDoc } from 'firebase/firestore';

async function getWorkoutSession(sessionId: string) {
  const docRef = doc(db, 'workoutSessions', sessionId);
  const docSnap = await getDoc(docRef);
  
  if (!docSnap.exists()) {
    throw new Error('Session not found');
  }
  
  return { id: docSnap.id, ...docSnap.data() } as WorkoutSession;
}
```

**Query Collection**:
```typescript
import { collection, query, where, orderBy, getDocs } from 'firebase/firestore';

async function getUserSessions(userId: string) {
  const q = query(
    collection(db, 'workoutSessions'),
    where('userId', '==', userId),
    orderBy('date', 'desc')
  );
  
  const snapshot = await getDocs(q);
  return snapshot.docs.map(doc => ({
    id: doc.id,
    ...doc.data()
  })) as WorkoutSession[];
}
```

### Updating Data

**Update Workout Type**:
```typescript
import { doc, updateDoc, serverTimestamp } from 'firebase/firestore';

async function updateWorkoutType(id: string, updates: Partial<WorkoutTypeInput>) {
  const docRef = doc(db, 'workoutTypes', id);
  
  await updateDoc(docRef, {
    ...updates,
    updatedAt: serverTimestamp(),
  });
}
```

**Update Workout Session**:
```typescript
async function updateWorkoutSession(id: string, updates: Partial<WorkoutSessionInput>) {
  const docRef = doc(db, 'workoutSessions', id);
  
  await updateDoc(docRef, {
    ...updates,
    updatedAt: serverTimestamp(),
  });
}
```

### Deleting Data

**Delete Workout Type**:
```typescript
import { doc, deleteDoc } from 'firebase/firestore';

async function deleteWorkoutType(id: string) {
  const docRef = doc(db, 'workoutTypes', id);
  await deleteDoc(docRef);
}
```

**Delete Workout Session**:
```typescript
async function deleteWorkoutSession(id: string) {
  const docRef = doc(db, 'workoutSessions', id);
  await deleteDoc(docRef);
}
```

### Real-Time Listeners

**Listen to Workout Types**:
```typescript
import { collection, query, where, onSnapshot } from 'firebase/firestore';

function subscribeToWorkoutTypes(userId: string, callback: (types: WorkoutType[]) => void) {
  const q = query(
    collection(db, 'workoutTypes'),
    where('userId', '==', userId)
  );
  
  const unsubscribe = onSnapshot(q, (snapshot) => {
    const types = snapshot.docs.map(doc => ({
      id: doc.id,
      ...doc.data()
    })) as WorkoutType[];
    
    callback(types);
  });
  
  // Return unsubscribe function to clean up
  return unsubscribe;
}
```

## Validation

### Zod Schemas

All form inputs are validated with Zod schemas before submission.

**Workout Type Schema**:
```typescript
import { z } from 'zod';

export const workoutTypeSchema = z.object({
  name: z.string()
    .min(1, 'Name is required')
    .max(100, 'Name must be less than 100 characters'),
  category: z.nativeEnum(WorkoutCategory),
  equipment: z.nativeEnum(EquipmentType),
  muscleGroups: z.array(z.nativeEnum(MuscleGroup))
    .min(1, 'Select at least one muscle group'),
  activityTags: z.array(z.nativeEnum(ActivityTag)),
});

export type WorkoutTypeFormData = z.infer<typeof workoutTypeSchema>;
```

**Workout Session Schema**:
```typescript
export const setEntrySchema = z.object({
  reps: z.number().int().min(1).optional(),
  weight: z.number().min(0).optional(),
  rpe: z.number().int().min(1).max(10).optional(),
  distance: z.number().min(0).optional(),
  time: z.number().int().min(0).optional(),
});

export const exerciseEntrySchema = z.object({
  workoutTypeId: z.string().min(1, 'Exercise is required'),
  sets: z.array(setEntrySchema).min(1, 'Add at least one set'),
  notes: z.string().optional(),
});

export const workoutSessionSchema = z.object({
  date: z.date(),
  duration: z.number().int().min(1, 'Duration must be at least 1 minute'),
  exercises: z.array(exerciseEntrySchema).min(1, 'Add at least one exercise'),
  notes: z.string().optional(),
});

export type WorkoutSessionFormData = z.infer<typeof workoutSessionSchema>;
```

## Error Handling

### Firebase Errors

Common Firebase errors and how to handle them:

```typescript
try {
  await createWorkoutSession(data);
} catch (error) {
  if (error.code === 'permission-denied') {
    // User doesn't have permission
    toast.error('You do not have permission to perform this action');
  } else if (error.code === 'not-found') {
    // Document doesn't exist
    toast.error('Workout session not found');
  } else if (error.code === 'unauthenticated') {
    // User is not authenticated
    router.push('/login');
  } else {
    // Generic error
    toast.error('An error occurred. Please try again.');
    console.error(error);
  }
}
```

### Form Validation Errors

React Hook Form automatically displays Zod validation errors:

```tsx
<form onSubmit={handleSubmit(onSubmit)}>
  <input {...register('name')} />
  {errors.name && (
    <p className="text-red-500 text-sm">{errors.name.message}</p>
  )}
</form>
```

## Optimistic Updates

For better UX, use SWR's optimistic updates:

```typescript
import { mutate } from 'swr';

async function deleteWorkoutType(id: string) {
  // Optimistically update the UI
  mutate(['workoutTypes', user.uid], 
    (current) => current?.filter(type => type.id !== id),
    false // Don't revalidate yet
  );
  
  try {
    // Perform actual deletion
    await deleteDoc(doc(db, 'workoutTypes', id));
    
    // Revalidate to ensure consistency
    mutate(['workoutTypes', user.uid]);
  } catch (error) {
    // Rollback on error
    mutate(['workoutTypes', user.uid]);
    throw error;
  }
}
```

## Data Aggregation

### Calculate Total Volume

```typescript
function calculateTotalVolume(session: WorkoutSession): number {
  return session.exercises.reduce((total, exercise) => {
    const exerciseVolume = exercise.sets.reduce((sum, set) => {
      if (set.weight && set.reps) {
        return sum + (set.weight * set.reps);
      }
      return sum;
    }, 0);
    return total + exerciseVolume;
  }, 0);
}
```

### Calculate Personal Records

```typescript
function calculatePRs(sessions: WorkoutSession[], workoutTypeId: string) {
  let maxWeight = 0;
  let maxReps = 0;
  let maxVolume = 0;
  
  sessions.forEach(session => {
    session.exercises
      .filter(ex => ex.workoutTypeId === workoutTypeId)
      .forEach(exercise => {
        exercise.sets.forEach(set => {
          if (set.weight && set.weight > maxWeight) {
            maxWeight = set.weight;
          }
          if (set.reps && set.reps > maxReps) {
            maxReps = set.reps;
          }
          if (set.weight && set.reps) {
            const volume = set.weight * set.reps;
            if (volume > maxVolume) {
              maxVolume = volume;
            }
          }
        });
      });
  });
  
  return { maxWeight, maxReps, maxVolume };
}
```

## Rate Limiting

Firebase has built-in rate limiting, but for additional protection:

1. **Client-side debouncing**: Prevent rapid-fire requests
2. **SWR deduplication**: Automatic request deduplication
3. **Firebase App Check**: Verify requests come from legitimate app

## Best Practices

### 1. Always Check Authentication

```typescript
const { user } = useAuth();

if (!user) {
  return <div>Please log in</div>;
}
```

### 2. Use TypeScript Types

```typescript
// Good: Type-safe
const session: WorkoutSession = await getSession(id);

// Bad: No type safety
const session = await getSession(id);
```

### 3. Handle Loading States

```typescript
const { data, isLoading, error } = useWorkoutTypes();

if (isLoading) return <Skeleton />;
if (error) return <Error />;
if (!data) return null;

return <List data={data} />;
```

### 4. Validate User Input

```typescript
// Always validate with Zod before submitting
const result = workoutTypeSchema.safeParse(formData);

if (!result.success) {
  console.error(result.error);
  return;
}

await createWorkoutType(result.data);
```

### 5. Clean Up Listeners

```typescript
useEffect(() => {
  const unsubscribe = onSnapshot(query, callback);
  
  // Clean up on unmount
  return () => unsubscribe();
}, []);
```

## Future API Enhancements

See [ideas.md](./ideas.md) for planned features:

- **REST API**: Custom endpoints for complex operations
- **GraphQL**: Flexible data fetching with Apollo
- **WebSockets**: Real-time collaboration features
- **Cloud Functions**: Server-side data processing
- **Analytics API**: Advanced progress analytics

---

This API documentation covers all current data models and operations in WorkTrack. For setup instructions, see [SETUP.md](./SETUP.md).
