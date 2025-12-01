# Twenty CRM - Replit Setup

## Project Overview
Twenty is an open-source CRM platform built with:
- **Frontend**: React + Vite + TypeScript (Nx monorepo)
- **Backend**: NestJS + PostgreSQL + Redis + BullMQ
- **Package Manager**: Bun (migrated from Yarn 4)

## Current Setup Status

### Completed Setup
1. PostgreSQL database created and configured
2. Redis server installed and running
3. Environment files configured (.env files in both packages)
4. Vite configuration updated to allow all hosts (for Replit proxy)
5. Frontend configured to run on port 5000 with host 0.0.0.0
6. Node.js upgraded to v24.4.0
7. **Migrated from Yarn 4 to Bun** for faster, more reliable package management
8. Dependencies installed successfully with Bun

### Package Manager Migration Notes

The project was originally configured for Yarn 4 workspaces, but was migrated to Bun due to:
- Yarn 4 link step consistently hanging in Replit's Nix environment
- Node version enforcement issues (project required ^24.5.0)

**Changes Made for Bun Compatibility**:
- Removed `.yarnrc.yml` and `packageManager` field from package.json
- Changed `resolutions` to `overrides` in package.json
- Removed yarn `patch:` protocol references from dependencies (using regular versions instead)
- Updated workspace configuration to simpler array format
- Updated engine requirements to be more flexible (node >=20.0.0)
- Hardcoded locale constants in `lingui.config.ts` (temporary workaround until twenty-shared build is fixed)

## Project Structure
```
/
├── packages/
│   ├── twenty-front/     # React frontend (port 5000) - RUNNING
│   ├── twenty-server/    # NestJS backend (port 3000)
│   ├── twenty-ui/        # Shared UI components
│   ├── twenty-emails/    # Email templates
│   ├── twenty-shared/    # Shared utilities
│   ├── twenty-cli/       # CLI tools
│   └── ...other packages
├── .env files configured
└── PostgreSQL + Redis running
```

## Environment Variables
- **Frontend**: `REACT_APP_PORT=5000`, `VITE_HOST=0.0.0.0`
- **Backend**: `PG_DATABASE_URL=$DATABASE_URL`, `REDIS_URL=redis://localhost:6379`

## Running the Application

### Frontend (Workflow)
The frontend runs automatically via the "Twenty CRM Frontend" workflow which executes:
```bash
cd packages/twenty-front && node ../../node_modules/vite/bin/vite.js --port 5000 --host 0.0.0.0
```

### Install Dependencies
```bash
bun install
```

### Building Shared Packages (If Needed)
Before running the full application, shared packages may need to be built:
```bash
cd packages/twenty-shared && npx vite build
cd packages/twenty-ui && npx vite build
```

Note: The barrel generation scripts have IPC issues with tsx in Replit, so vite build may generate empty chunks. A workaround was applied to lingui.config.ts to hardcode the locale constants.

### Full Stack (Backend Not Yet Configured)
To run the full stack, the backend needs additional setup:
1. Run database migrations
2. Start the NestJS server on port 3000
3. Configure Redis connections

## Known Limitations

1. **Shared package builds**: The `twenty-shared` package requires barrel generation scripts that have IPC issues with tsx in Replit. A temporary workaround was applied to `lingui.config.ts` to hardcode locale constants. Once the twenty-shared package builds properly, this should be reverted to import from `twenty-shared/translations`.

2. **Patch dependencies removed**: The following Yarn patch: protocol dependencies were converted to regular versions:
   - `react-phone-number-input@3.4.5`
   - `@graphql-yoga/nestjs@2.1.0`
   - `@nestjs/graphql@12.1.1`
   - `@ptc-org/nestjs-query-graphql@4.2.0`
   - `typeorm@0.3.20`
   
   Some packages may behave slightly differently without the original patches.

3. **Backend**: Not yet fully configured - requires additional setup for database migrations and Redis.

## User Preferences
- Package manager: Bun (migrated from Yarn)
- Frontend port: 5000
- Host binding: 0.0.0.0 (required for Replit proxy)
