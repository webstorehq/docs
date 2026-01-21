# Web Store Code Style Guide

## General Principles

- **TypeScript** — strict typing is required
- **Functional approach** — prefer arrow functions
- **Component approach** — reusable components
- **Comments in English** — all code comments must be in English

## TypeScript

### Typing Rules

```typescript
// ✅ Good: explicit typing
const handleClick = (id: string): void => {
  // ...
};

// ❌ Bad: any
const handleClick = (id: any) => {
  // ...
};

// ✅ Good: interfaces for objects
interface App {
  id: string;
  name: string;
  slug: string;
}

// ✅ Good: types for union types
type AppStatus = 'draft' | 'pending_review' | 'published' | 'unpublished';
```

### Imports

```typescript
// ✅ Good: grouped imports
import { useState, useEffect } from 'react';
import { useTranslation } from 'react-i18next';

import { Button } from '@/components/ui/button';
import { useAppQuery } from '@/store';

// ❌ Bad: mixed imports
import { useState } from 'react';
import { Button } from '@/components/ui/button';
import { useEffect } from 'react';
```

## React Patterns

### Components

```typescript
// ✅ Good: functional component with arrow function
export const AppCard = ({ app }: { app: App }) => {
  return <div>{app.name}</div>;
};

// ✅ Good: named export
export const AppCard = ({ app }: AppCardProps) => {
  // ...
};

// ❌ Bad: function declaration (unless required)
export function AppCard({ app }: AppCardProps) {
  // ...
}
```

### Hooks

```typescript
// ✅ Good: custom hooks with use prefix
export const useAppData = (id: string) => {
  const { data, loading } = useAppQuery(id);
  return { app: data, loading };
};

// ✅ Good: memoization of expensive computations
const filteredApps = useMemo(() => {
  return apps.filter(app => app.status === 'published');
}, [apps]);
```

### Conditional Rendering

```typescript
// ✅ Good: early return
if (!app) return null;

// ✅ Good: ternary operator for simple cases
{isLoading ? <Skeleton /> : <AppList apps={apps} />}

// ✅ Good: logical operator for conditional rendering
{isAdmin && <AdminPanel />}
```

## Project Structure

### Atomic Design / Feature-Sliced

The project uses a hybrid approach:

```
src/
├── components/          # Reusable components
│   ├── ui/             # Base UI components (shadcn)
│   ├── AppDetails/     # Components for app page
│   └── ...
├── pages/              # Application pages
├── features/           # Feature modules (if needed)
├── store/              # State management
│   ├── atoms/          # Jotai atoms
│   ├── queries/        # GraphQL queries
│   └── mutations/      # GraphQL mutations
├── hooks/              # Reusable hooks
├── utils/              # Utilities (one function per file)
└── lib/                # Library configuration
```

### File Naming

- **Components**: `PascalCase.tsx` (e.g., `AppCard.tsx`)
- **Utilities**: `camelCase.ts` (e.g., `formatDate.ts`)
- **Hooks**: `useCamelCase.ts` (e.g., `useAppData.ts`)
- **Types**: `camelCase.ts` or in `types/index.ts`

### Component Organization

```typescript
// ✅ Good: component in separate folder with index.ts
// components/AppCard/
//   ├── AppCard.tsx
//   └── index.ts

// index.ts
export { AppCard } from './AppCard';

// ✅ Good: simple component in single file
// components/AppCard.tsx
```

## State Management

### Jotai (Local State)

```typescript
// ✅ Good: atoms in separate files
// store/atoms/ui.ts
export const themeAtom = atom<'light' | 'dark'>('light');

// ✅ Good: using atoms via hooks
const [theme, setTheme] = useAtom(themeAtom);
```

### Apollo Client (Server State)

```typescript
// ✅ Good: queries in separate files
// store/queries/schemas/app.ts
export const GET_APP = gql`
  query GetApp($id: UUID!) {
    appsCollection(filter: { id: { eq: $id } }) {
      edges {
        node {
          id
          name
        }
      }
    }
  }
`;

// ✅ Good: hooks for queries
// store/queries/hooks/app.ts
export const useAppQuery = (id: string) => {
  return useQuery(GET_APP, { variables: { id } });
};
```

## Styling

### Tailwind CSS

```typescript
// ✅ Good: using Tailwind classes
<div className="flex items-center gap-4 p-6 bg-white rounded-lg">

// ✅ Good: conditional classes via cn()
import { cn } from '@/utils';

<div className={cn(
  'base-classes',
  isActive && 'active-classes',
  className
)}>

// ❌ Bad: inline styles (except for dynamic values)
<div style={{ color: 'red' }}>  // ❌
<div style={{ color: theme.color }}>  // ✅ OK for dynamics
```

## Commit Format

### Conventional Commits

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Commit Types

- `feat`: new feature
- `fix`: bug fix
- `docs`: documentation changes
- `style`: formatting, missing semicolons, etc.
- `refactor`: code refactoring
- `perf`: performance improvement
- `test`: adding tests
- `chore`: dependency updates, configuration

### Examples

```
feat(app): add app installation functionality

fix(auth): resolve token refresh issue

docs(readme): update installation instructions

refactor(components): extract AppCard to separate component
```

## Code Examples

### Component with Props

```typescript
interface AppCardProps {
  app: App;
  onInstall?: (appId: string) => void;
  className?: string;
}

export const AppCard = ({ app, onInstall, className }: AppCardProps) => {
  const { t } = useTranslation();
  
  const handleInstall = () => {
    onInstall?.(app.id);
  };

  return (
    <div className={cn('app-card', className)}>
      <img src={app.icon_url} alt={app.name} />
      <h3>{app.name}</h3>
      <Button onClick={handleInstall}>
        {t('install')}
      </Button>
    </div>
  );
};
```

### Hook with State

```typescript
export const useAppInstallation = (appId: string) => {
  const [isInstalling, setIsInstalling] = useState(false);
  const { addInstalledApp } = useAddInstalledAppMutation();

  const install = async () => {
    setIsInstalling(true);
    try {
      await addInstalledApp(appId);
      toast.success('App installed');
    } catch (error) {
      toast.error('Installation failed');
    } finally {
      setIsInstalling(false);
    }
  };

  return { install, isInstalling };
};
```

### Utility

```typescript
// utils/formatDate.ts
export const formatDate = (date: Date | string): string => {
  const d = typeof date === 'string' ? new Date(date) : date;
  return d.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  });
};
```

## Backend Structure

### Supabase Functions

```
supabase/
├── functions/          # Edge Functions
│   └── stripe-webhook/
├── migrations/         # Database migrations
├── schema.sql         # Current schema (read-only)
├── functions.sql      # DB functions (read-only)
└── policies.sql       # RLS policies (read-only)
```

### API Endpoints (Vercel Functions)

```
api/
├── app-meta.ts        # Meta tags for SEO
├── rss.xml.ts        # RSS feed
└── ...
```

## Code Review

### ESLint

The project uses ESLint with rules:
- TypeScript rules
- React Hooks rules
- Imports and order

### Before Committing

1. Check linter: `yarn lint`
2. Check types: TypeScript compilation
3. Ensure code follows project style

