# Missions

An Android app that gives you one social challenge per day — talk to a stranger, compliment someone, message an old friend. Complete it to build your streak, fail it and it resets. Simple, accountable, social.

Built with Kotlin and Jetpack Compose.

---

## Features

- **Daily challenge** — a new mission is assigned every 24 hours via WorkManager
- **Streak tracking** — consecutive daily completions build your streak, shown with a flame icon
- **Social score** — a persistent score that increases on completion and decreases on failure
- **Challenge history** — view all past completed and failed challenges with dates
- **Custom challenges** — add your own challenges and optionally share them with the community
- **Daily reminder notifications** — a WorkManager-scheduled notification fires at 4pm if the challenge hasn't been completed
- **Offline-first** — all data persisted locally with Room (SQLite)
- **Backend integration** — Retrofit-based API for user accounts, login/signup, and community challenge sharing

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Kotlin |
| UI | Jetpack Compose, Material3 |
| Local storage | Room (SQLite), DataStore |
| Background tasks | WorkManager |
| Networking | Retrofit, Gson |
| Navigation | Navigation Compose |

---

## Architecture

```
missions/
├── MainActivity.kt                  # Entry point, permission handling, WorkManager scheduling
├── HomeScreen.kt                    # Main screen, nav host, all shared composables
│
├── screens/
│   ├── AddScreen.kt                 # Add custom challenge UI
│   ├── HistoryScreen.kt             # Past challenges list
│   ├── ProfileScreen.kt             # Profile / login entry point
│   ├── LoginScreen.kt               # Login form
│   ├── SignupScreen.kt              # Signup form
│   └── MoreScreen.kt                # Settings / extra screen
│
├── data/
│   ├── Mission.kt                   # Room entity: active missions table
│   ├── HistoryMission.kt            # Room entity: completed/failed missions table
│   ├── PostMission.kt               # DTO for posting missions to server
│   ├── User.kt                      # DTO for login/signup
│   ├── DataSource.kt                # Seed data for initial database population
│   └── repository/
│       └── MissionRepository.kt     # Room data access layer
│
├── network/
│   ├── MissionsApi.kt               # Retrofit singleton
│   └── MissionsApiService.kt        # API endpoint definitions
│
└── workers/
    ├── NewMissionWorker.kt          # Daily mission rotation (24h periodic)
    └── NotificationWorker.kt        # Daily reminder notification (fires at 4pm)
```

### Data Flow

On first launch, `DataSource` seeds the Room database with the bundled challenge list. The current mission ID and state are persisted in DataStore. Each day at midnight, `NewMissionWorker` evaluates whether the previous mission was completed or failed, updates the social score and streak accordingly, writes a `HistoryMission` record, and assigns the next mission.

---

## Building

### Prerequisites

- Android Studio Hedgehog or later
- Android SDK 26+
- A running instance of the Missions backend server (for login/signup/community features)

### Setup

1. Clone the repository
2. Open in Android Studio
3. Update `BASE_URL` in `MissionsApi.kt` to point to your server
4. Build and run on a device or emulator (`Run > Run 'app'`)

> **Note:** The app functions fully offline without a backend — login, signup, and community challenge sharing require a server connection.

---

## Roadmap

- [ ] Replace hardcoded `BASE_URL` with a build config variable
- [ ] Complete `ConfirmationDialog` composable (currently empty)
- [ ] Implement profile screen for logged-in users
- [ ] Streak saver mechanic (referenced in UI but not implemented)
- [ ] Fix `confirmedPassword` showing mismatch error before user finishes typing
- [ ] Migrate `currentMission` state to a `StateFlow` in a ViewModel
- [ ] Add `delay(200)` workaround fix in `LaunchedEffect`
