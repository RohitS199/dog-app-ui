# PawCheck

**AI-powered dog health triage for pet owners.** Describe your dog's symptoms and get an educational urgency classification — Emergency, Urgent, Soon, or Low Urgency — along with what to tell your vet and veterinary source citations.

> **Important:** PawCheck provides educational health guidance only. It is **not** a substitute for professional veterinary advice, diagnosis, or treatment. Always consult your veterinarian.

## The Golden Rule

> Never let a dog owner walk away from a genuine emergency thinking they can wait.

Every feature in PawCheck exists in service of this principle.

## Features

- **Symptom Triage** — Describe symptoms in plain language, get an AI-generated urgency assessment with color-coded results
- **Real-Time Emergency Detection** — Client-side keyword engine flags potential emergencies *before* you even submit, with immediate emergency vet resources
- **Dog Profiles** — Store multiple dogs with breed, age, weight, and vet phone number
- **Veterinary Source Citations** — Every triage result includes tiered references from veterinary institutions
- **What to Tell Your Vet** — Actionable bullet points to prepare for your vet visit
- **Account Management** — Change password, delete account (with full data anonymization)
- **Offline Awareness** — Detects network status and gracefully handles offline scenarios
- **Accessibility** — WCAG AA compliant with 48dp touch targets, screen reader support, and proper contrast ratios

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | React Native 0.81 (Expo SDK 54, TypeScript strict) |
| Navigation | Expo Router v6 (file-based routing) |
| State | Zustand v5 (3 stores: auth, dog, triage) |
| Backend | Supabase (Auth, Postgres, Edge Functions, pgvector RAG) |
| Auth Storage | expo-secure-store (device secure enclave) |
| Testing | Jest 29 + React Native Testing Library (103 tests, 7 suites) |

## Quick Start

### Prerequisites

- Node.js 18+
- npm or yarn
- Expo CLI (`npx expo`)
- iOS Simulator (macOS) or Android Emulator

### Setup

```bash
# Clone the repository
git clone https://github.com/RohitS199/dog-app-ui.git
cd dog-app-ui

# Install dependencies
npm install

# Configure environment
cp .env.example .env
# Edit .env with your Supabase credentials:
#   EXPO_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
#   EXPO_PUBLIC_SUPABASE_ANON_KEY=your-anon-key-here

# Start the development server
npx expo start
```

### Running Tests

```bash
npm test              # Run all 103 tests
npx jest --no-cache   # Clear cache and run
```

## Project Structure

```
dog_app_ui/
├── app/                    # Expo Router screens
│   ├── (auth)/             # Sign in, sign up, forgot password
│   ├── (tabs)/             # Home, triage, settings
│   ├── terms.tsx           # Terms of Service acceptance
│   ├── add-dog.tsx         # Add dog profile
│   ├── edit-dog.tsx        # Edit/delete dog profile
│   ├── emergency.tsx       # Emergency resources
│   ├── change-password.tsx # Change password
│   └── delete-account.tsx  # Account deletion
├── src/
│   ├── components/
│   │   ├── legal/          # Safety-critical components (5)
│   │   └── ui/             # General UI components (7)
│   ├── constants/          # Theme, config, loading tips
│   ├── hooks/              # useAppState, useNetworkStatus
│   ├── lib/                # Supabase client, emergency keywords
│   ├── stores/             # Zustand stores
│   └── types/              # TypeScript API contract types
├── DOCUMENTATION.md        # Comprehensive project docs
└── CLAUDE.md               # AI assistant instructions
```

## How Triage Works

```
User Input ──► Client-Side Emergency Detection (500ms debounce)
                         │
                    [if emergency keywords found]
                         │
                    Emergency Alert Banner
                         │
               User can still submit ──► check-symptoms Edge Function
                                                  │
                                         16-Step Pipeline:
                                         1. JWT validation
                                         2. Rate limiting (10/hr)
                                         3. Emergency bypass (instant)
                                         4. Off-topic detection
                                         5. Dog profile lookup
                                         6. RAG retrieval (303 chunks)
                                         7. LLM triage generation
                                         8. Output safety filter
                                         9. Foreign body urgency floor
                                         10. Audit logging
                                                  │
                                         ┌────────┼────────┐
                                         │        │        │
                                      Triage  Emergency  Off-Topic
                                      Result   Bypass    Response
```

## Urgency Levels

| Level | Color | Meaning |
|-------|-------|---------|
| Emergency | Red (#C62828) | Seek veterinary care immediately |
| Urgent | Orange (#E65100) | See a vet within 24 hours |
| Soon | Amber (#F57C00) | Schedule a vet visit this week |
| Low Urgency | Teal (#00897B) | Monitor at home, see vet if worsening |

> The lowest urgency uses **teal**, not green — a deliberate choice to avoid the "green = all clear" false safety signal.

## Backend

The backend runs on Supabase with three Edge Functions:

| Function | Purpose |
|----------|---------|
| `check-symptoms` (v10) | 16-step triage pipeline with RAG, LLM, and safety filters |
| `delete-account` (v1) | Password re-auth, data anonymization, account deletion |
| `run-stress-test` (v3) | 120-prompt automated test harness |

### Stress Test Results

- **Tier 1 (Safety):** 100% (60/60) — zero safety-critical failures
- **Tier 2 (Quality):** 90% (54/60)
- **Overall:** 95% (114/120)

## Security

- Row Level Security (RLS) on all database tables
- JWT tokens stored in device secure enclave via expo-secure-store
- Edge Functions validate JWTs internally
- Audit log is append-only (no UPDATE/DELETE policies)
- Account deletion anonymizes triage data before removing user
- COPPA 13+ age gate on sign-up
- All SQL functions have immutable search_path

## Test Coverage

103 tests across 7 suites:

| Suite | Tests | Coverage |
|-------|-------|----------|
| emergencyKeywords | 39 | Pattern matching, normalization, clusters |
| foreignBodyFloor | 22 | Urgency floor logic, regex patterns, edge cases |
| triageStore | 13 | Symptoms, char limits, nudge tracking |
| TriageResult | 10 | Triage display, emergency bypass, sources |
| LegalComponents | 9 | Disclaimer, call banner, source citation |
| UrgencyBadge | 7 | All urgency levels, accessibility |
| EmergencyAlert | 3 | Rendering, dismiss callback |

## Known Issues

- **50/day rate limit** not implemented (10/hour exists)
- **Leaked password protection** requires Supabase Pro Plan
- **Buddy mascot animation** deferred (libraries installed, not implemented)
- **Emergency vet locator** opens Google search (Google Places API is post-MVP)

## License

All rights reserved. This is a private project.

## Contact

Rohit Sandur — [GitHub](https://github.com/RohitS199)
