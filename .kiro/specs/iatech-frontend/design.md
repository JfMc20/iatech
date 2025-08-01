# Design Document - IATECH Frontend

## Overview

El frontend de IATECH será una aplicación web moderna construida con Next.js 14, TypeScript, Tailwind CSS y Shadcn/ui. La arquitectura seguirá principios de Atomic Design y feature-based organization para garantizar escalabilidad, mantenibilidad y reutilización de componentes.

## Architecture

### Technology Stack

#### Core Framework
- **Next.js 14** con App Router para SSR/SSG y optimización automática
- **TypeScript** para type safety y mejor developer experience
- **React 18** con Concurrent Features y Suspense

#### Styling & UI
- **Tailwind CSS** para utility-first styling consistente
- **Shadcn/ui** para componentes pre-built de alta calidad
- **Radix UI** como base para componentes accesibles
- **Lucide React** para iconografía consistente
- **Framer Motion** para animaciones fluidas

#### State Management
- **TanStack Query (React Query)** para server state management
- **Zustand** para client state management
- **React Hook Form** para form state

#### Data Fetching & Validation
- **Axios** con interceptors para HTTP requests
- **Zod** para schema validation
- **@hookform/resolvers** para integración con React Hook Form

#### Development Tools
- **ESLint** + **Prettier** para code quality
- **Husky** para git hooks
- **TypeScript** strict mode
- **Storybook** para component documentation

### Project Structure

```
iatech-frontend/
├── app/                          # Next.js App Router
│   ├── (auth)/                   # Auth route group
│   │   ├── login/
│   │   │   ├── page.tsx
│   │   │   └── loading.tsx
│   │   ├── register/
│   │   │   ├── page.tsx
│   │   │   └── loading.tsx
│   │   ├── forgot-password/
│   │   └── layout.tsx            # Auth layout
│   ├── (dashboard)/              # Protected dashboard routes
│   │   ├── admin/
│   │   │   ├── packages/
│   │   │   │   ├── page.tsx      # Package management
│   │   │   │   ├── create/
│   │   │   │   └── [id]/edit/
│   │   │   ├── inquiries/
│   │   │   │   ├── page.tsx      # Inquiry management
│   │   │   │   └── [id]/
│   │   │   ├── analytics/
│   │   │   │   └── page.tsx      # Analytics dashboard
│   │   │   └── layout.tsx        # Admin layout
│   │   ├── client/
│   │   │   ├── dashboard/
│   │   │   │   └── page.tsx      # Client dashboard
│   │   │   ├── projects/
│   │   │   │   ├── page.tsx      # Project list
│   │   │   │   └── [id]/
│   │   │   ├── profile/
│   │   │   │   └── page.tsx      # Profile management
│   │   │   └── layout.tsx        # Client layout
│   │   └── layout.tsx            # Dashboard layout
│   ├── (public)/                 # Public routes
│   │   ├── packages/
│   │   │   ├── page.tsx          # Package catalog
│   │   │   └── [id]/
│   │   │       └── page.tsx      # Package details
│   │   ├── services/
│   │   │   └── page.tsx          # Services overview
│   │   ├── about/
│   │   │   └── page.tsx          # About page
│   │   ├── contact/
│   │   │   └── page.tsx          # Contact page
│   │   ├── inquiry/
│   │   │   └── page.tsx          # Custom inquiry form
│   │   └── layout.tsx            # Public layout
│   ├── api/                      # API routes (middleware)
│   │   ├── auth/
│   │   │   └── route.ts          # Auth middleware
│   │   └── upload/
│   │       └── route.ts          # File upload proxy
│   ├── globals.css               # Global styles
│   ├── layout.tsx                # Root layout
│   ├── page.tsx                  # Home page
│   ├── loading.tsx               # Global loading UI
│   ├── error.tsx                 # Global error UI
│   └── not-found.tsx             # 404 page
├── components/                   # Reusable components
│   ├── ui/                       # Shadcn/ui base components
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   ├── input.tsx
│   │   ├── form.tsx
│   │   ├── dialog.tsx
│   │   ├── dropdown-menu.tsx
│   │   ├── table.tsx
│   │   ├── badge.tsx
│   │   ├── avatar.tsx
│   │   ├── skeleton.tsx
│   │   ├── toast.tsx
│   │   └── index.ts              # Barrel exports
│   ├── layout/                   # Layout components
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   ├── Sidebar.tsx
│   │   ├── Navigation.tsx
│   │   ├── MobileMenu.tsx
│   │   └── index.ts
│   ├── forms/                    # Form components
│   │   ├── LoginForm.tsx
│   │   ├── RegisterForm.tsx
│   │   ├── InquiryForm.tsx
│   │   ├── PackageForm.tsx
│   │   ├── ProfileForm.tsx
│   │   └── index.ts
│   ├── features/                 # Feature-specific components
│   │   ├── packages/
│   │   │   ├── PackageCard.tsx
│   │   │   ├── PackageGrid.tsx
│   │   │   ├── PackageFilters.tsx
│   │   │   ├── PackageSearch.tsx
│   │   │   ├── PackageDetails.tsx
│   │   │   └── index.ts
│   │   ├── inquiries/
│   │   │   ├── InquiryCard.tsx
│   │   │   ├── InquiryList.tsx
│   │   │   ├── InquiryStatus.tsx
│   │   │   ├── QuoteForm.tsx
│   │   │   └── index.ts
│   │   ├── dashboard/
│   │   │   ├── StatsCard.tsx
│   │   │   ├── RecentActivity.tsx
│   │   │   ├── QuickActions.tsx
│   │   │   └── index.ts
│   │   └── auth/
│   │       ├── AuthGuard.tsx
│   │       ├── RoleGuard.tsx
│   │       └── index.ts
│   ├── common/                   # Common components
│   │   ├── LoadingSpinner.tsx
│   │   ├── ErrorBoundary.tsx
│   │   ├── Pagination.tsx
│   │   ├── SearchBar.tsx
│   │   ├── FileUpload.tsx
│   │   ├── ImageGallery.tsx
│   │   ├── ConfirmDialog.tsx
│   │   └── index.ts
│   └── providers/                # Context providers
│       ├── AuthProvider.tsx
│       ├── ThemeProvider.tsx
│       ├── QueryProvider.tsx
│       └── index.ts
├── lib/                          # Utilities and configuration
│   ├── api.ts                    # Axios configuration
│   ├── auth.ts                   # Auth utilities
│   ├── utils.ts                  # Utility functions
│   ├── constants.ts              # App constants
│   ├── validations.ts            # Zod schemas
│   ├── types.ts                  # Shared TypeScript types
│   └── config.ts                 # App configuration
├── hooks/                        # Custom React hooks
│   ├── useAuth.ts
│   ├── useApi.ts
│   ├── useLocalStorage.ts
│   ├── useDebounce.ts
│   ├── useInfiniteScroll.ts
│   └── index.ts
├── store/                        # Zustand stores
│   ├── authStore.ts
│   ├── uiStore.ts
│   ├── filtersStore.ts
│   └── index.ts
├── styles/                       # Additional styles
│   ├── components.css
│   └── utilities.css
├── types/                        # TypeScript type definitions
│   ├── api.ts
│   ├── auth.ts
│   ├── package.ts
│   ├── inquiry.ts
│   └── index.ts
└── public/                       # Static assets
    ├── images/
    ├── icons/
    └── favicon.ico
```

## Components and Interfaces

### Component Architecture (Atomic Design)

#### Atoms (components/ui/)
Base components from Shadcn/ui with consistent styling and behavior:
- Button, Input, Card, Badge, Avatar
- Typography components
- Icon components
- Loading states

#### Molecules (components/common/)
Combinations of atoms for specific functionality:
- SearchBar (Input + Button + Icon)
- Pagination (Buttons + Text)
- FileUpload (Input + Progress + Preview)

#### Organisms (components/features/)
Complex components that combine molecules and atoms:
- PackageGrid (Cards + Filters + Pagination)
- InquiryForm (Multiple inputs + validation)
- Dashboard sections

#### Templates (app/*/page.tsx)
Page layouts that combine organisms:
- HomePage, PackagesPage, DashboardPage

### Key Component Interfaces

#### PackageCard Component
```typescript
interface PackageCardProps {
  package: ServicePackage;
  variant?: 'default' | 'compact' | 'featured';
  onSelect?: (packageId: string) => void;
  showActions?: boolean;
  className?: string;
}
```

#### InquiryForm Component
```typescript
interface InquiryFormProps {
  onSubmit: (data: InquiryFormData) => Promise<void>;
  initialData?: Partial<InquiryFormData>;
  isLoading?: boolean;
  className?: string;
}
```

#### Dashboard Layout
```typescript
interface DashboardLayoutProps {
  children: React.ReactNode;
  sidebar?: React.ReactNode;
  header?: React.ReactNode;
  role: 'admin' | 'client';
}
```

## Data Models

### Frontend Data Types

#### User Interface
```typescript
interface User {
  id: string;
  email: string;
  role: 'admin' | 'client';
  profile: {
    firstName: string;
    lastName: string;
    company?: string;
    phone?: string;
    avatar?: string;
  };
  createdAt: string;
  lastLogin?: string;
}
```

#### Service Package Interface
```typescript
interface ServicePackage {
  id: string;
  name: string;
  description: string;
  detailedDescription: string;
  category: 'web-design' | 'graphic-design' | 'brand-identity' | 'ai-automation';
  level: 'basic' | 'intermediate' | 'advanced';
  price: number;
  currency: string;
  features: string[];
  imageUrl?: string;
  gallery?: string[];
  isActive: boolean;
  deliveryTime: number;
  revisions: number;
  createdAt: string;
  updatedAt: string;
}
```

#### Custom Inquiry Interface
```typescript
interface CustomInquiry {
  id: string;
  clientInfo: {
    name: string;
    email: string;
    company?: string;
    phone?: string;
  };
  projectDetails: {
    type: string;
    description: string;
    budget: {
      min: number;
      max: number;
      currency: string;
    };
    timeline: string;
    requirements: string[];
    attachments?: File[];
  };
  status: 'pending' | 'reviewing' | 'quoted' | 'accepted' | 'rejected';
  adminNotes?: string;
  quote?: {
    amount: number;
    currency: string;
    deliveryTime: number;
    terms: string;
  };
  createdAt: string;
  updatedAt: string;
}
```

## Error Handling

### Error Boundary Strategy
- Global ErrorBoundary for unhandled errors
- Feature-specific error boundaries
- Graceful degradation for non-critical features
- User-friendly error messages

### API Error Handling
```typescript
interface ApiError {
  message: string;
  code: string;
  details?: Record<string, string[]>;
  statusCode: number;
}

// Error handling in components
const handleApiError = (error: ApiError) => {
  switch (error.statusCode) {
    case 401:
      // Redirect to login
      break;
    case 403:
      // Show permission error
      break;
    case 422:
      // Show validation errors
      break;
    default:
      // Show generic error
  }
};
```

### Form Validation Strategy
- Client-side validation with Zod schemas
- Real-time validation feedback
- Server-side validation error display
- Consistent error message formatting

## Testing Strategy

### Testing Pyramid

#### Unit Tests (70%)
- Component logic testing with React Testing Library
- Utility function testing with Jest
- Custom hooks testing with @testing-library/react-hooks

#### Integration Tests (20%)
- API integration testing
- Form submission flows
- Authentication flows
- Page navigation testing

#### E2E Tests (10%)
- Critical user journeys with Playwright
- Cross-browser compatibility
- Performance testing

### Testing Tools
- **Jest** for unit testing
- **React Testing Library** for component testing
- **MSW (Mock Service Worker)** for API mocking
- **Playwright** for E2E testing
- **Storybook** for component documentation and visual testing

## Performance Optimization

### Next.js Optimizations
- **Static Site Generation (SSG)** for public pages
- **Server-Side Rendering (SSR)** for dynamic content
- **Incremental Static Regeneration (ISR)** for updated content
- **Image Optimization** with next/image
- **Font Optimization** with next/font

### Code Splitting Strategy
- **Route-based splitting** (automatic with Next.js)
- **Component-based splitting** with React.lazy()
- **Library splitting** for large dependencies
- **Preloading** critical resources

### Caching Strategy
- **Browser caching** for static assets
- **Service Worker** for offline functionality
- **React Query caching** for API responses
- **Local Storage** for user preferences

### Bundle Optimization
- **Tree shaking** for unused code elimination
- **Code minification** in production
- **Gzip compression** for assets
- **Modern JavaScript** with appropriate polyfills

## Security Considerations

### Authentication Security
- **HttpOnly cookies** for token storage
- **CSRF protection** for forms
- **XSS prevention** with proper sanitization
- **Secure headers** configuration

### Data Protection
- **Input sanitization** for all user inputs
- **Output encoding** for dynamic content
- **File upload validation** and scanning
- **Sensitive data masking** in UI

### Route Protection
- **Authentication guards** for protected routes
- **Role-based access control** for admin features
- **Session management** with automatic logout
- **Audit logging** for sensitive actions

This design provides a solid foundation for building a scalable, maintainable, and high-performance frontend application that integrates seamlessly with the IATECH backend API.

## Extended Architecture for Ecosystem Features

### New Component Categories

#### Service Discovery Components
```
components/features/discovery/
├── ServiceDiscoveryQuiz.tsx      # Interactive assessment quiz
├── RecommendationEngine.tsx      # AI-powered recommendations
├── BusinessAssessment.tsx        # ROI and timeline analysis
├── RoadmapVisualizer.tsx         # Implementation roadmap display
└── index.ts
```

#### Client Hub Components
```
components/features/client-hub/
├── ResourceDashboard.tsx         # Main hub dashboard
├── DocumentManager.tsx           # Document organization
├── AssetLibrary.tsx             # Brand assets management
├── VersionControl.tsx           # File versioning system
├── CollaborationSpace.tsx       # Real-time collaboration
└── index.ts
```

#### Integration Management
```
components/features/integrations/
├── IntegrationHub.tsx           # Integration management center
├── GoogleWorkspaceConnector.tsx # Google integration
├── GitHubConnector.tsx          # GitHub integration
├── NotionConnector.tsx          # Notion integration
├── WhatsAppConnector.tsx        # WhatsApp integration
├── IntegrationStatus.tsx        # Connection status monitoring
└── index.ts
```

#### Collaboration Features
```
components/features/collaboration/
├── WorkspaceManager.tsx         # Workspace management
├── RealTimeEditor.tsx           # Collaborative editing
├── TaskManager.tsx              # Task and milestone tracking
├── CommunicationHub.tsx         # Integrated communication
├── PresenceIndicator.tsx        # User presence display
└── index.ts
```

#### Analytics & Insights
```
components/features/analytics/
├── AnalyticsDashboard.tsx       # Main analytics view
├── ProjectMetrics.tsx           # Project progress metrics
├── IntegrationAnalytics.tsx     # Integration usage analytics
├── AIInsights.tsx               # ML-powered insights
├── ReportGenerator.tsx          # Automated reporting
└── index.ts
```

### Extended Data Models

#### Integration Models
```typescript
interface Integration {
  id: string;
  type: 'google-workspace' | 'github' | 'notion' | 'whatsapp';
  userId: string;
  config: IntegrationConfig;
  status: 'connected' | 'disconnected' | 'error';
  lastSync: string;
  permissions: string[];
  createdAt: string;
  updatedAt: string;
}

interface IntegrationConfig {
  credentials: EncryptedCredentials;
  settings: Record<string, any>;
  webhooks?: WebhookConfig[];
  syncPreferences: SyncPreferences;
}
```

#### Workspace Models
```typescript
interface Workspace {
  id: string;
  name: string;
  type: 'project' | 'collaboration' | 'resource-hub';
  ownerId: string;
  members: WorkspaceMember[];
  resources: WorkspaceResource[];
  integrations: string[];
  settings: WorkspaceSettings;
  createdAt: string;
  updatedAt: string;
}

interface WorkspaceMember {
  userId: string;
  role: 'owner' | 'admin' | 'member' | 'viewer';
  permissions: Permission[];
  joinedAt: string;
}
```

#### Document Management Models
```typescript
interface Document {
  id: string;
  name: string;
  type: string;
  size: number;
  url: string;
  thumbnailUrl?: string;
  workspaceId: string;
  uploadedBy: string;
  versions: DocumentVersion[];
  tags: string[];
  metadata: DocumentMetadata;
  permissions: DocumentPermission[];
  createdAt: string;
  updatedAt: string;
}

interface DocumentVersion {
  id: string;
  version: string;
  url: string;
  changes: string;
  createdBy: string;
  createdAt: string;
}
```

#### Real-time Collaboration Models
```typescript
interface CollaborationSession {
  id: string;
  workspaceId: string;
  participants: Participant[];
  activeDocument?: string;
  cursors: CursorPosition[];
  selections: Selection[];
  changes: Change[];
  startedAt: string;
  lastActivity: string;
}

interface Participant {
  userId: string;
  name: string;
  avatar?: string;
  status: 'active' | 'idle' | 'away';
  cursor?: CursorPosition;
  selection?: Selection;
}
```

### Real-time Architecture

#### WebSocket Integration
```typescript
// Real-time event handling
interface RealtimeEvent {
  type: 'cursor-move' | 'selection-change' | 'document-edit' | 'user-join' | 'user-leave';
  payload: any;
  userId: string;
  workspaceId: string;
  timestamp: string;
}

// WebSocket hook for real-time features
export function useRealtimeCollaboration(workspaceId: string) {
  const [socket, setSocket] = useState<WebSocket | null>(null);
  const [participants, setParticipants] = useState<Participant[]>([]);
  const [events, setEvents] = useState<RealtimeEvent[]>([]);

  // Connection management, event handling, etc.
}
```

#### State Management for Real-time
```typescript
// Zustand store for collaboration state
interface CollaborationStore {
  activeWorkspace: string | null;
  participants: Participant[];
  cursors: CursorPosition[];
  selections: Selection[];
  changes: Change[];
  
  // Actions
  joinWorkspace: (workspaceId: string) => void;
  leaveWorkspace: () => void;
  updateCursor: (position: CursorPosition) => void;
  applyChange: (change: Change) => void;
}
```

### Integration Security Architecture

#### OAuth2 Flow Management
```typescript
interface OAuthProvider {
  name: string;
  clientId: string;
  scopes: string[];
  authUrl: string;
  tokenUrl: string;
  refreshUrl: string;
}

// OAuth hook for secure integration setup
export function useOAuthIntegration(provider: OAuthProvider) {
  const [isConnecting, setIsConnecting] = useState(false);
  const [isConnected, setIsConnected] = useState(false);
  const [error, setError] = useState<string | null>(null);

  // OAuth flow implementation
}
```

#### Token Management
```typescript
interface TokenManager {
  store: (provider: string, tokens: TokenSet) => Promise<void>;
  retrieve: (provider: string) => Promise<TokenSet | null>;
  refresh: (provider: string) => Promise<TokenSet>;
  revoke: (provider: string) => Promise<void>;
}
```

### Performance Considerations for Extended Features

#### Code Splitting Strategy
- **Integration modules**: Lazy load integration components
- **Collaboration features**: Load only when workspace is active
- **Analytics**: Load charts and heavy visualizations on demand
- **Document viewer**: Progressive loading for large files

#### Caching Strategy
- **Integration data**: Cache with TTL based on provider limits
- **Document metadata**: Aggressive caching with invalidation
- **Collaboration state**: Memory cache with persistence
- **Analytics data**: Time-based caching with background refresh

#### Real-time Optimization
- **Event batching**: Batch cursor movements and selections
- **Conflict resolution**: Operational transformation for document edits
- **Connection management**: Automatic reconnection with exponential backoff
- **Presence optimization**: Throttle presence updates

This extended architecture maintains the solid foundation while adding the necessary components and patterns for the ecosystem features.