# SmartFrame Scraper

## Overview
Professional image metadata extraction tool for SmartFrame search results. This application allows users to scrape image metadata from SmartFrame.com search URLs, extract detailed information, and export results in JSON or CSV format.

## Project Structure
- **Frontend**: React + Vite + Tailwind CSS + Radix UI components
- **Backend**: Express.js server with TypeScript
- **Database**: PostgreSQL (production/Replit) with SQLite fallback (local development)
- **Scraper**: Puppeteer-based web scraper with configurable options

## Key Features
- Bulk URL scraping support (up to 50 URLs per request)
- Rate limiting to prevent abuse
- Real-time progress tracking via WebSocket
- Configurable scraping options (max images, auto-scroll, concurrency)
- Canvas extraction for high-quality images
- Metadata normalization (title, subject, tags, comments, authors, date taken, copyright)
- Export to JSON or CSV formats
- **Advanced VPN IP Rotation System**:
  - Support for NordVPN and Windscribe CLI
  - Multiple rotation strategies (manual, time-based, count-based, adaptive)
  - Automatic IP tracking and verification
  - Secure command execution (prevents command injection)
  - User-friendly configuration UI

## Technology Stack
- **Frontend**: 
  - React 18
  - Vite (dev server on port 5000)
  - TailwindCSS v4
  - Wouter (routing)
  - TanStack Query (data fetching)
  - Radix UI (components)
  
- **Backend**:
  - Express.js
  - TypeScript
  - Drizzle ORM
  - Puppeteer (web scraping)
  - WebSocket (real-time updates)
  
- **Database**:
  - PostgreSQL (via @neondatabase/serverless for Replit)
  - SQLite (better-sqlite3 for local development)

## Development Setup
The application is configured to run on port 5000 with the following workflow:
- Command: `npm run dev`
- Output: Webview (accessible via Replit preview)
- Frontend + Backend served together from the same port

## Database Configuration
- Uses PostgreSQL when `DATABASE_URL` is set (Replit environment)
- Falls back to SQLite (`./data/local.db`) for local development
- Schema migrations via Drizzle Kit: `npm run db:push`

## Code Quality & Status

### ✅ **All TypeScript Errors Fixed** (November 21, 2025)
- **Status**: Zero TypeScript compilation errors
- **Previously**: 200+ errors due to schema misalignment and type issues
- **Fixed**: Complete schema alignment, proper type assertions, and configuration handling
- **Result**: Application compiles cleanly and runs without errors

### Known Minor Issues
- **Neon Serverless Driver Bug**: The @neondatabase/serverless v0.10.4 has a known bug when processing empty result sets. This is handled with try-catch wrappers in the storage layer that gracefully return empty arrays.
- **PostCSS Warning**: A harmless warning about PostCSS `from` option appears during builds but doesn't affect functionality.

## API Endpoints
- `POST /api/scrape/start` - Start a single scrape job
- `POST /api/scrape/bulk` - Start multiple scrape jobs
- `GET /api/scrape/jobs` - Get all scrape jobs
- `GET /api/scrape/job/:jobId` - Get a specific job with results
- `GET /api/export/:jobId?format=json|csv` - Export job results

## Configuration Files
- `scraper.config.json` - Scraper settings (VPN, wait times, concurrency, metadata timeouts)
- `vite.config.ts` - Frontend dev server configuration (port 5000, host 0.0.0.0)
- `drizzle.config.ts` - Database migrations configuration
- `tsconfig.json` - TypeScript configuration

## Performance Optimizations (November 20, 2025)
### Bundle Size Reduction
- **Removed 72 unused npm packages** including:
  - Multiple @radix-ui components (radio-group, sidebar, checkbox, menubar, etc.)
  - Heavy libraries (framer-motion, recharts, react-resizable-panels)
  - Development tools and unused dependencies
- **CSS bundle reduced by 23%**: 52.21 kB → 40.23 kB (9.00 kB → 7.29 kB gzipped)
- **File count reduced by 30 files** (27 unused UI components + 3 root files)

### Code Splitting & Lazy Loading
- Implemented React.lazy() for route-based code splitting
- Added Suspense boundaries for smooth loading experience
- Lazy-loaded NotFound page reduces initial bundle size
- Manual chunk splitting strategy:
  - `react-vendor`: React core libraries (243.78 kB / 78.76 kB gzipped)
  - `ui-vendor`: Radix UI components
  - `form`: Form handling libraries (62.18 kB / 14.88 kB gzipped)
  - `router`: Wouter routing (3.18 kB / 1.65 kB gzipped)
  - `icons`: Lucide React icons
  - `vendor`: Other dependencies (86.16 kB / 27.54 kB gzipped)

### React Performance
- Added useMemo for filtered images to prevent unnecessary recalculations
- Added useCallback for event handlers to prevent unnecessary re-renders
- Optimized component re-rendering with proper memoization

### Build Optimizations
- Terser minification with aggressive compression
- Production console.log removal
- CSS code splitting enabled
- Dynamic chunk strategy to avoid referencing removed packages
- Source maps disabled for smaller production builds

### UX Improvements
- Centralized export functionality through ExportModal
- Custom radio button implementation to avoid reinstalling dependencies
- Cleaner component structure with reduced duplication

### Results
- **Final bundle**: 11 optimized chunks for better caching
- **Faster initial load**: Lazy-loaded routes reduce startup time
- **Better caching**: Vendor code separated from app code
- **Smaller downloads**: Users only download what they need

## VPN IP Rotation System (November 20, 2025)

### Overview
The scraper includes a comprehensive VPN IP rotation system to avoid rate limiting and detection. Supports multiple VPN providers and rotation strategies.

### Supported VPN Providers
1. **NordVPN CLI** - Fully automated with `nordvpn` command
2. **Windscribe CLI** - Fully automated with `windscribe` command
3. **Manual Mode** - For any VPN (prompts user to change manually)

### Rotation Strategies
- **Manual**: IP changes only when requested
- **Count-based**: Rotate after X successful scrapes
- **Time-based**: Rotate every X minutes
- **Adaptive**: Combines failures, count, and time (Recommended)

### Configuration Example (scraper.config.json)
```json
{
  "vpn": {
    "enabled": true,
    "clientType": "nordvpn",
    "rotationStrategy": "adaptive",
    "rotationCount": 500,
    "rotationIntervalMs": 3600000,
    "changeAfterFailures": 5,
    "serverList": ["United_States", "Canada", "uk2435"]
  }
}
```

### Security Features
- **Command Injection Prevention**: Uses `spawn()` for secure execution
- **Three-Stage Verification**: Process success → Network connectivity → IP change
- **IP Tracking**: Monitors and logs IP changes
- **Audit Trail**: All rotation events logged

### UI Component
- Configuration panel: `client/src/components/scraper/vpn-config-panel.tsx`
- User-friendly interface with tooltips and validation
- Real-time feedback and status indicators

## Recent Changes

### November 21, 2025 - Fresh GitHub Import to Replit Setup Complete
1. **Environment Setup**:
   - Fresh clone from GitHub repository
   - Installed all npm dependencies (536 packages)
   - Created comprehensive .gitignore for Node.js project
   - Configured workflow "Start application" to run on port 5000 with webview output
   - Verified PostgreSQL database connection (DATABASE_URL configured)
   - Application running successfully with frontend + backend on port 5000
   - Deployment configured for VM target with build (`npm run build`) and run (`npm start`) commands

2. **Critical Bug Fixes**:
   
   a. **Database Insertion Error** (High Priority):
   - **Fixed**: "RangeError: Too few parameter values were provided" error that was causing scrapes to fail after successful completion
   - **Root Cause**: The `createdAt` field was missing from the database insert operation in `server/storage.ts`
   - **Impact**: Users were losing all scraped data at the final save step after spending time scraping multiple URLs
   - **Solution**: Added explicit `createdAt: new Date()` field to the image insert mapping (line 148)
   - **Note**: While the database schema has a default value for `createdAt`, Drizzle ORM with better-sqlite3 wasn't applying it properly when the field wasn't explicitly provided

   b. **Schema Alignment Issues** (High Priority):
   - **Fixed**: TypeScript compilation errors and runtime issues due to code using obsolete database field names
   - **Root Cause**: The database schema was cleaned up to 7 metadata fields (titleField, subjectField, tags, comments, authors, dateTaken, copyright), but the scraper code was still using old field names (photographer, title, matchEvent, caption, city, country, date, featuring, imageSize, fileSize, contentPartner, etc.)
   - **Impact**: 200+ TypeScript errors, potential runtime crashes, and incorrect data mapping
   - **Files Fixed**:
     - `client/src/components/scraper/image-card.tsx` - Updated UI to display new schema fields
     - `server/scraper.ts` - Completely rewrote `parseMetadata()` and `extractImageData()` functions to map SmartFrame data to the 7 clean metadata fields
     - `server/scraper.ts` - Fixed `isEmptyResult()` helper to check new fields
     - `server/scraper.ts` - Removed unused `generateCaption` import
     - `server/scraper.ts` - Removed obsolete field merge logic (lines 2347-2357)
     - `server/scraper.ts` - Removed legacy `image.date` normalization that referenced deleted field
   - **Mappings Applied**:
     - `photographer` → `authors`
     - `title`/`event`/`headline` → `titleField`
     - `featuring`/`people`/`subject` → `subjectField`
     - `caption`/`description` → `comments`
     - `date` → `dateTaken` (with normalization)
     - `tags` → now a comma-separated string instead of array
     - Removed fields: `city`, `country`, `imageSize`, `fileSize`, `matchEvent`, `contentPartner`
   
   c. **Type Safety Improvements**:
   - **Fixed**: Type mismatches and unsafe type assertions throughout the codebase
   - **Changes Made**:
     - Updated `SmartframeMetadata` interface to support nullable fields (`title`, `caption`, `contentPartner`) and added `nextData?: any` field
     - Fixed Date→string conversions: `completedAt: new Date()` → `completedAt: new Date().toISOString()`
     - Properly typed `cachedData` and `rawData.nextData` with type assertions (`as any`) where dynamic data is expected
     - Fixed VPN configuration: Merged partial config with `VPNManager.createDefaultConfig()` instead of unsafe `as any` cast
     - Fixed WaitTime configuration: Transformed `scrollDelay` to `baseDelay` before passing to `WaitTimeHelper`
     - Fixed database type: Added proper type assertion for Neon database client compatibility
     - Removed hardcoded cookie selector reference to non-existent config field
   - **Result**: Zero TypeScript compilation errors, improved type safety, no runtime type failures

3. **Dependencies and Configuration**:
   - Installed missing package: `@radix-ui/react-separator` for UI components
   - Vite dev server properly configured (host: 0.0.0.0, port: 5000, allowedHosts: true)
   - Express server configured to serve both API and frontend on port 5000
   - VPN configuration properly initialized with default values merged with user config
   - Wait time helper properly configured with correct field mappings
   - PostgreSQL database connection working
   - All existing features and optimizations preserved from previous setup

### November 20, 2025
1. Added PostgreSQL database support with proper schema migration
2. Fixed Neon serverless driver bug with error handling wrappers
3. Configured deployment for production (VM deployment target)
5. Implemented comprehensive performance optimizations (see above)
6. **Implemented Advanced VPN IP Rotation System**:
   - Secure command execution using `spawn()` (prevents command injection)
   - Support for NordVPN and Windscribe CLI
   - Multiple rotation strategies (manual, time-based, count-based, adaptive)
   - Three-stage connection verification
   - IP tracking and audit logging
   - User-friendly configuration UI component
7. **Enhanced Scraping Performance & Sequential Processing** (November 20, 2025):
   - Increased maximum concurrency from 10 to 20 tabs for faster bulk scraping
   - Implemented ordered sequential mode for reliable SmartFrame rendering
   - Added configurable inter-tab delays (3-5 seconds default, 1-10 seconds range)
   - Automatic tab activation (bringToFront) ensures each tab is active for proper GPU rendering
   - Random delays between tabs prevent detection and improve reliability
   - New UI controls in config panel for ordered sequential mode and delay configuration
   - Reduced SmartFrame render wait times (19s → 5s initial, 10s → 3s post-resize)
   - System intelligently caps concurrency at maxConcurrency limit (20 by default)
   - All configuration exposed through user-friendly UI with conditional visibility

## Deployment
- Type: VM (stateful, maintains server state)
- Build: `npm run build`
- Run: `npm start`
- Port: 5000 (automatically configured)

## User Preferences
- None documented yet

## Notes
- The application is designed to scrape SmartFrame.com search results only
- Duplicate URLs are automatically filtered in bulk requests
- Rate limiting: configurable per client IP
- All scraped data is stored with normalized metadata for easy CSV export
