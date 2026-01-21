# Web Store Product

## Product Value

### For Users

Web Store provides users with the ability to:

1. **Install PWA applications** like native apps
   - Unified web application catalog
   - One-click installation (for non-Chromium browsers, a few-click instruction)
   - Standalone mode operation

2. **Manage installed applications**
   - Library of all installed PWAs
   - Automatic updates
   - Favorite app tracking

3. **Discover quality applications**
   - Ratings and reviews
   - Web Store recommendations
   - Categories and search

### For Developers

Web Store provides developers with:

1. **Easy publishing**
   - Simple app addition process
   - Manifest import
   - Multilingual support

2. **Analytics**
   - App views
   - Installation count

3. **Features**
   - Smart installation banner via `<script>`
   - Set of promotional pages about PWA benefits

## User Scenarios

### 1. Browsing the Catalog

**User** opens Web Store and sees:
- Main page with recommended apps
- App categories
- Catalog search

**Path**: Home → Category → App List

### 2. PWA Installation

**User** finds an interesting app:
1. Opens the app page
2. Views description, screenshots, reviews
3. Clicks "Install"
4. Browser shows installation dialog / installation instructions for their browser
5. Installation dialog / instructions can be transferred to the developer's client in one connection
6. App installs and appears in the library and on the desktop

**Path**: Catalog → App Page → Installation → Library

### 3. App Publishing (Developer)

**Developer** publishes their PWA:
1. Registers a developer account
2. Simple verification
3. Creates a new app:
   - Fills in basic information
   - Uploads icon and screenshots
   - Specifies manifest URL
   - Adds translations
4. Submits for moderation (status: `pending_review`)
5. Administrator reviews and approves
6. App is published (status: `published`)

**Path**: Registration → Verification → Creation → Moderation → Publishing

### 4. App Management

**Developer** manages a published app:
1. Views analytics (views, installations)
2. Updates information
3. Adds new translations
4. Manages status (published/hidden)

**Path**: Developer Dashboard → Select App → Edit

## Target Audience

### Primary Users

1. **Web application users**
   - Looking for quality PWAs
   - Want to install web apps as native
   - Need a centralized catalog

2. **Owners of older devices**
   - Cannot install native applications
   - Need lightweight PWA solutions

3. **iOS users**
   - Limited access to App Store in some regions
   - Alternative way to install applications

### Developers

1. **Independent developers**
   - Publish their PWAs
   - Need a platform for distribution

2. **Companies**
   - Corporate PWAs
   - Internal applications

3. **Startups**
   - Quick launch without App Store/Play Market
   - MVP testing

## Key Differentiators

### From App Store / Play Market

1. **Web technologies**
   - No native development required
   - Works on any platform
   - Single codebase for all devices

2. **Fast publishing**
   - No lengthy moderation process
   - Instant updates
   - No publishing fees

3. **Openness**
   - Access to source code (web)
   - Application transparency
   - No platform restrictions

### From Other PWA Catalogs

1. **Native-like installation**
   - Smart install button suggests the right URL (direct installation/app's own instructions/platform instructions)

2. **Features**
   - Providing a simple unified installation flow for users even on Safari
   - Ability to not write a BeforeInstallPrompt handler at all - just connect ours

3. **Developer-oriented**
   - Simple publishing process
   - Analytics and metrics
   - App form auto-fill tools (Manifest/AppStore/ruStore)

## Success Metrics

### For Users
- Number of installed apps
- App usage time
- Satisfaction (reviews)

### For Developers
- Number of published apps
- Number of installations

### For Platform
- Total number of apps
- Active users
- Catalog growth

