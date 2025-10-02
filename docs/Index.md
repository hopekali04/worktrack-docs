# 💪 WorkTrack Documentation

## Overview

WorkTrack is a comprehensive, production-ready workout tracking application that helps users log workouts, track progress, and visualize their fitness journey. Built with modern web technologies, it provides a seamless experience across all devices with real-time data synchronization and robust security.

The application follows a mobile-first design philosophy with accessibility built in from the ground up. Every feature is designed to be intuitive, fast, and reliable, making it easy for users to focus on their fitness goals rather than fighting with technology.

## Table of Contents

- [Index.md](./Index.md) - Project overview and key features (this document)
- [Architecture.md](./Architecture.md) - System design and component structure
- [API.md](./API.md) - Data models, hooks, and Firebase integration
- [SETUP.md](./SETUP.md) - Local development and deployment guide
- [ideas.md](./ideas.md) - Future features and enhancements

## Key Features

### 🔐 Authentication & User Management
- **Email/Password Authentication**: Secure user registration and login with Firebase Auth
- **Google Sign-In**: One-click authentication with Google accounts
- **User Profiles**: Personalized settings including unit system, dark mode, and rest time preferences
- **Session Management**: Persistent authentication with automatic token refresh

### 💪 Workout Management
- **Custom Workout Types**: Create and manage unlimited exercise types
- **Default Exercise Library**: Pre-loaded with common exercises across all major muscle groups
- **Categorization**: Organize exercises by category (upper, lower, full body, cardio, core)
- **Equipment Tracking**: Tag exercises by equipment type (barbell, dumbbell, machine, etc.)
- **Muscle Group Mapping**: Track which muscles each exercise targets

### 📊 Session Logging
- **Detailed Workout Sessions**: Log date, duration, exercises, sets, reps, weight, and more
- **RPE Tracking**: Rate of Perceived Exertion for monitoring training intensity
- **Distance & Time Metrics**: Support for cardio activities (running, cycling, swimming)
- **Session Notes**: Add contextual notes about how you felt, form cues, or observations
- **Multi-Exercise Support**: Log complex workouts with multiple exercises and varying sets

### 📈 Progress Tracking & Analytics
- **Interactive Charts**: Visualize progress with responsive charts using Recharts
- **Volume Tracking**: Monitor total training volume over time
- **Frequency Analysis**: See workout consistency and patterns
- **Personal Records**: Automatic tracking of personal bests (coming soon)
- **Trend Analysis**: Identify improvements and plateaus across different time periods

### 🎨 User Experience
- **Mobile-First Design**: Optimized for touch interfaces and small screens
- **Dark Mode Support**: Automatic theme detection with manual override
- **Responsive Layout**: Seamless experience from mobile phones to desktop monitors
- **Accessible UI**: WCAG 2.1 compliant with keyboard navigation and screen reader support
- **Loading States**: Skeleton screens and optimistic updates for smooth interactions

### 🔒 Security & Privacy
- **Multi-User Isolation**: Firestore security rules ensure complete data separation
- **Server-Side Validation**: All data validated on Firebase before storage
- **Type-Safe Operations**: Strict TypeScript prevents runtime errors
- **Secure Authentication**: Industry-standard auth with bcrypt password hashing
- **HTTPS Only**: All data transmitted over encrypted connections

### 🛠️ Developer Experience
- **TypeScript Strict Mode**: Catch errors at compile time, not runtime
- **Zod Validation**: Runtime type checking with clear error messages
- **React Hook Form**: Performant forms with built-in validation
- **SWR Caching**: Automatic request deduplication and optimistic updates
- **Hot Module Replacement**: See changes instantly during development

## Technologies Used

### Frontend Framework
- **Next.js 15.5.4** - React framework with App Router for file-based routing
- **React 19.1.0** - Modern React with concurrent features
- **TypeScript 5** - Static typing for improved code quality and developer experience

### Styling & UI
- **Tailwind CSS 3.4** - Utility-first CSS framework for rapid UI development
- **Headless UI 2.2** - Unstyled, fully accessible UI components
- **Heroicons 2.2** - Beautiful hand-crafted SVG icons
- **PostCSS 8** - CSS transformation and optimization

### Backend & Database
- **Firebase 12.3.0** - Backend-as-a-Service platform
  - **Firebase Authentication** - User authentication and authorization
  - **Cloud Firestore** - NoSQL document database with real-time sync
  - **Cloud Storage** - File storage for user uploads (future use)
- **Firestore Security Rules** - Server-side data access control

### Forms & Validation
- **React Hook Form 7.63** - Performant, flexible form management
- **Zod 4.1** - TypeScript-first schema validation
- **@hookform/resolvers 5.2** - Integration between React Hook Form and Zod

### Data Fetching & State
- **SWR 2.3** - React Hooks for data fetching with caching
- **React Context API** - Global state management for auth and settings
- **Firebase Realtime Listeners** - Live data synchronization

### Data Visualization
- **Recharts 3.2** - Composable charting library built on D3
- **date-fns 4.1** - Modern date utility library for formatting and manipulation

### Development Tools
- **ESLint 9** - Code linting with Next.js config
- **Turbopack** - Fast bundler for development and production builds
- **Autoprefixer** - Automatic vendor prefixing for CSS

### Deployment & Hosting
- **Vercel** - Optimal Next.js hosting with automatic deployments
- **Firebase Hosting** - Alternative hosting option with CDN

## Project Philosophy

### Mobile-First Design
Every feature is designed and tested on mobile devices first, then progressively enhanced for larger screens. This ensures the app works perfectly where users need it most - at the gym.

### Type Safety
Strict TypeScript combined with Zod validation provides type safety from the UI layer through to the database. This catches bugs before they reach production and makes refactoring safe.

### Performance
The app uses SWR for intelligent caching and request deduplication, skeleton screens for perceived performance, and code splitting to minimize initial load times.

### Accessibility
Built with semantic HTML, ARIA attributes, keyboard navigation, and screen reader support. All interactive elements are focusable and have appropriate labels.

### Developer Experience
Clean code organization, comprehensive TypeScript types, and consistent naming conventions make the codebase easy to understand and maintain.

## Getting Started

New to WorkTrack development? Start with:

1. [SETUP.md](./SETUP.md) - Get your development environment running
2. [Architecture.md](./Architecture.md) - Understand the system design
3. [API.md](./API.md) - Learn about data models and hooks
4. [ideas.md](./ideas.md) - Explore future enhancements

## Contributing

Contributions are welcome! This project follows industry best practices:

- Strict TypeScript with no `any` types
- Comprehensive Zod validation for all user inputs
- Responsive, accessible UI components
- Security-first Firebase rules
- Clean, maintainable code with clear separation of concerns

## License

MIT License - feel free to use this project for personal or commercial purposes.

---

Built with ❤️ using Next.js, TypeScript, and Firebase
