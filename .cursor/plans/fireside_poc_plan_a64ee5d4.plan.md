---
name: Fireside POC Plan
overview: Bootstrap the Fireside React Native (Expo) app and validate it runs on both iOS and Android devices, establishing the frontend foundation for subsequent phases.
todos: []
isProject: false
---

# Fireside POC Plan

## Scope (from README Phase 0)

- Create a basic React Native app
- Install and run on both iOS and Android devices

No backend, auth, or real features—just validate the mobile development environment and device deployment.

---

## Current State

The repo contains only [README.md](README.md), [.gitignore](.gitignore), and git metadata. No `frontend/`, `backend/`, or `infra/` directories exist yet. The README defines the target structure:

- **frontend/** — React Native (Expo)
- **backend/** — Lambda + API (not in POC scope)
- **infra/** — Terraform (not in POC scope)

---

## Implementation Plan

### 1. Bootstrap Expo app in `frontend/`

Use the Expo CLI to create a new project inside `frontend/`:

```bash
npx create-expo-app@latest frontend --template default
```

**Why `default` template:**

- Expo Router + TypeScript (matches README: "Expo Router for navigation", "TypeScript")
- Provides `_layout.tsx` and file-based routing, ready for Phase 1 screens
- Includes `react-native-screens`, `react-native-safe-area-context`, and basic navigation

**Alternative:** `blank-typescript` if you prefer a minimal setup with a single `App.tsx` and add Router later. The default template is recommended to avoid restructuring in Phase 1.

### 2. Align app identity

Update `frontend/app.json` (or `app.config.js`):

- `name`: "Fireside"
- `slug`: "fireside"
- `scheme`: "fireside" (for future deep linking)

### 3. Add a minimal first screen

Replace the default Expo Router home screen (`app/index.tsx` or `src/app/index.tsx`) with a simple Fireside-branded welcome screen:

- App title "Fireside"
- One-line tagline (e.g., "Keep track of scheduled calls with friends")
- No navigation, no tabs—single screen only

This confirms the app renders correctly and establishes basic styling.

### 4. Add `frontend/.gitignore`

Create a `.gitignore` inside the frontend project (the Expo template may include one; add or ensure these entries):

- `.expo/`
- `dist/`
- `build/`
- `*.jks`, `*.p8`, `*.p12` (if present)
- `.env*.local` (for future env vars)

Keeps Expo/React Native–specific ignores scoped to the frontend; root [.gitignore](.gitignore) stays minimal.

### 5. Add `frontend/README.md`

Create a README inside the frontend project with run instructions:

**Prerequisites:**

- Node.js 18+
- npm or yarn
- Expo Go app on a physical device, or Xcode (iOS Simulator) / Android Studio (emulator)

**Commands:**

```bash
make install    # or: npm install
make run        # or: npx expo start
```

**Running on devices:**

- **iOS Simulator:** Press `i` in terminal, or run `npx expo start --ios`
- **Android Emulator:** Press `a` in terminal, or run `npx expo start --android`
- **Physical device:** Scan QR code with Expo Go (iOS Camera / Android Expo Go app)

Keeps frontend-specific docs scoped to the frontend; root [README.md](README.md) stays high-level.

### 6. Add `frontend/Makefile`

Create a Makefile with phony targets:

- `install` — `npm install` (install dependencies for dev)
- `install-ci` — `npm ci` (install dependencies for CI; uses lockfile)
- `run` — `npx expo start` (start dev server)
- `build` — `npx expo export` (export static bundle; validates build)

```makefile
.PHONY: install install-ci run build

install:
	npm install

install-ci:
	npm ci

run:
	npx expo start

build:
	npx expo export
```

Reference these targets in `frontend/README.md` as an alternative to running npm/npx directly. GitHub Actions (if added) can use `make install-ci` and `make build`.

### 7. Optional: GitHub Actions build check

A lightweight workflow to ensure the app builds on PR:

- **File:** `.github/workflows/build-frontend.yml`
- **Trigger:** PR/push to `main` when `frontend/**` changes
- **Steps:** `make install-ci`, `make build` (run from `frontend/`) to validate the project compiles
- No EAS submission or deployment in POC

---

## Out of Scope for POC


| Item                                 | Rationale            |
| ------------------------------------ | -------------------- |
| Backend / Lambda / API               | Phase 1              |
| Cognito / auth                       | Phase 1              |
| Terraform / AWS                      | Phase 1              |
| Push notifications                   | Phase 2              |
| Groups, Calls, real UI               | Phase 1              |
| EAS Build for App Store / Play Store | Can add in Phase 2–3 |


---

## Success Criteria

- `cd frontend && make install && make run` (or `npm install && npx expo start`) runs without errors
- App launches in iOS Simulator
- App launches in Android Emulator
- App launches on a physical device via Expo Go

---

## File Summary


| Action      | Path                                                                       |
| ----------- | -------------------------------------------------------------------------- |
| Create      | `frontend/` (entire Expo project via `create-expo-app`)                    |
| Edit        | `frontend/app.json` or `app.config.js` — name, slug, scheme                |
| Edit        | `frontend/app/index.tsx` (or `src/app/index.tsx`) — minimal welcome screen |
| Create/Edit | `frontend/.gitignore` — Expo-specific ignores (scoped to frontend)         |
| Create      | `frontend/README.md` — Getting Started with run instructions               |
| Create      | `frontend/Makefile` — install, install-ci, run, build (phony targets)      |
| Optional    | `.github/workflows/build-frontend.yml` — PR build check                    |
