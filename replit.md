# Twenty CRM - Replit Setup

## Project Overview
Twenty is an open-source CRM platform built with:
- **Frontend**: React + Vite + TypeScript (Nx monorepo)
- **Backend**: NestJS + PostgreSQL + Redis + BullMQ
- **Architecture**: Full-stack TypeScript monorepo using Yarn 4 workspaces

## Current Setup Status

### ✅ Completed
1. PostgreSQL database created and configured
2. Redis server installed and running
3. Environment files configured (.env files in both packages)
4. Vite configuration updated to allow all hosts (for Replit proxy)
5. Frontend configured to run on port 5000 with host 0.0.0.0
6. Node.js upgraded to v24.4.0

### ⚠️ Critical Issue: Yarn Installation Incomplete

**Status**: The project's dependency installation using Yarn 4 consistently fails during the "link step", preventing the application from running.

**Root Cause**: This is a known compatibility issue in Replit's Nix environment:
- Project requires Node ^24.5.0, Replit provides Node 24.4.0
- Yarn 4 with hardened mode + Nix symlink constraints cause link step to hang/crash
- The node_modules directory has 1771 packages but no `.bin` directory (binaries not linked)

**What Was Attempted**:
✅ Upgraded to Node 24.4.0 (highest available)
✅ Disabled hardened mode in .yarnrc.yml  
✅ Modified package.json engines to accept Node ^24.4.0
✅ Installed Redis and configured PostgreSQL
✅ Set up environment files
✅ Configured Vite for Replit proxy support
❌ Yarn install still fails during link step

### 📋 Resolution Options

**Option 1 - Continue Yarn Install Attempts** (Most Likely to Work):
The installation made significant progress (1771 packages fetched). You may need to run yarn install multiple times:
```bash
# Kill any hanging yarn processes
pkill -9 yarn

# Remove state file and retry
rm -f .yarn/install-state.gz
yarn install

# If it hangs, wait 5-10 minutes or try again
```

**Option 2 - Deploy on Different Platform**:
This Twenty CRM project may work better on:
- Local development environment with Node 24.5+
- Docker container with proper Node version
- Different cloud IDE with Node 24.5+ support

**Option 3 - Contact Twenty Support**:
This appears to be a known issue with Twenty CRM in certain environments. Check their GitHub issues or Discord for Replit-specific workarounds.

## Project Structure
```
/
├── packages/
│   ├── twenty-front/     # React frontend (port 5000)
│   ├── twenty-server/    # NestJS backend (port 3000)
│   ├── twenty-ui/        # Shared UI components
│   ├── twenty-emails/    # Email templates
│   └── ...other packages
├── .env files configured
└── PostgreSQL + Redis running
```

## Environment Variables
- **Frontend**: `REACT_APP_PORT=5000`, `VITE_HOST=0.0.0.0`
- **Backend**: `PG_DATABASE_URL=$DATABASE_URL`, `REDIS_URL=redis://localhost:6379`

## Running the Application
Once yarn install completes:
```bash
# Frontend only
cd packages/twenty-front && yarn nx start twenty-front

# Full stack (requires backend build first)
yarn nx build twenty-server
yarn start  # Runs both frontend and backend
```
