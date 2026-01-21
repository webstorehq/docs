# Web Store Architecture

## System Overview

Web Store is built as a modern PWA application with separation between frontend and backend components.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Frontend                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │   React 18   │  │    Vite      │  │  TypeScript  │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │    Jotai     │  │   Apollo     │  │ React Query  │       │
│  │  (State)     │  │  (GraphQL)   │  │   (Cache)    │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐   │
│  │         Service Worker (PWA)                         │   │
│  │  - Caching                                           │   │
│  │  - Offline support                                   │   │
│  │  - App updates                                       │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ HTTPS
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      Backend (Supabase)                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │ PostgreSQL   │  │   GraphQL    │  │     Auth     │       │
│  │  Database    │  │    API       │  │   (JWT)      │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │   Storage    │  │   RLS        │  │  Functions   │       │
│  │  (S3-like)   │  │ (Security)   │  │  (Edge)      │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
└─────────────────────────────────────────────────────────────┘
                            │
                            │
┌─────────────────────────────────────────────────────────────┐
│                    External Services                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │   Static     │  │   PayPal     │  │   YooMoney   │       │
│  │   Hosting    │  │  (Payments)  │  │  (Payments)  │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
└─────────────────────────────────────────────────────────────┘
```

## Frontend Architecture

### Technology Stack

- **React 18** — UI library
- **TypeScript** — type safety
- **Vite** — build tool and dev server
- **Tailwind CSS** — styling
- **shadcn/ui** — UI components

### State Management

**Jotai** — for local state:
- Atoms for UI state (theme, modals)
- Atoms for user data (profile, favorites)

**Apollo Client** — for server state:
- GraphQL queries to backend/database
- Data caching
- Optimistic updates

### PWA Functionality

**Service Worker** (Workbox):
- Static resource caching
- API request caching (StaleWhileRevalidate)
- Offline support
- Automatic updates

**Manifest**:
- App icons
- Installation screenshots
- Theme and colors
- Display mode: standalone

## Backend Architecture

### Database

**PostgreSQL Database**:
- Main tables: `apps`, `profiles`, `app_reviews`, `categories`
- Row Level Security (RLS) for security
- Triggers for automatic updates
- Functions for business logic

**GraphQL API**:
- Automatically generated from PostgreSQL schema
- Endpoint: `{BACKEND_URL}/graphql/v1`
- Authentication via JWT tokens

**Authentication**:
- Auth (JWT)
- Email/password
- OAuth providers (configurable)
- Sessions in localStorage

**Storage**:
- S3-like storage
- Buckets for app media files
- Public and private access

**API Functions**:
- Webhooks for payments
- API endpoints:
  - `app-meta` — meta tags/HTML tips for SEO
  - `rss.xml` — RSS feed generation
  - `sitemap.xml` — sitemap generation
  - `send-feedback` — feedback submission

## App Hosting

Each PWA application is stored as:
- **Manifest URL** — link to application's manifest.json
- **Install URL** — URL for installation
- **Icon URL** — application icon (cached in Supabase Storage)
- **Screenshots** — screenshots (cached in Supabase Storage)

When publishing an application, media files are copied to database Storage for reliability.

## Update Pipeline

1. **Versioning**: each application has a version in the manifest
2. **Update Detection**: Service Worker checks for updates every 5 minutes
3. **Cache Invalidation**: cache is cleared on update
4. **SW Update**: new Service Worker is activated automatically

## Security

### HTTPS
- All connections via HTTPS
- Required for PWA functionality
- Content Security Policy (CSP)

### Manifest Integrity
- Verification of application manifest integrity
- Manifest structure validation

### Code Signing
- Application signing for authenticity verification
- Protection against application forgery

### Row Level Security (RLS)
- Data protection at database level
- Access policies for each table
- Automatic filtering by users

## Scaling

### Scaling Plans
- CDN for static resources
- Edge caching
- Database query optimization
- Indexes for fast search

