# PocketWise 💰

A personal finance web app built for Nigerian secondary school students. PocketWise helps students track income and expenses, set savings goals, understand their spending habits, and get personalised financial advice from an AI coach.

---

## Features

- **Dashboard** — Financial health score (0–100), income/expense summary, balance, and recent transactions
- **Transaction Logger** — Log income and expenses by category, date, and description. Each expense is automatically classified as a *want* or a *need*
- **Wants vs Needs** — Visual breakdown of spending by category and want/need split, with a horizontal bar chart
- **Savings Goals** — Create goals with a target amount and deadline, track progress with a progress bar, and top up at any time
- **Reports** — Monthly summary with savings rate and a category spending chart
- **AI Coach** — Chat with a built-in financial coach powered by the Claude API. Falls back to a smart offline coach if the API is unavailable
- **Authentication** — Sign up / log in with email and password, or use **Continue with Google** (OAuth via Firebase)
- **Profile** — Edit display name and change password from the profile page
- **Dark mode** — Full light/dark theme toggle, saved per user
- **Cloud storage** — Google users have their data saved to Firestore (accessible from any device). Email/password users use localStorage

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML, CSS, JavaScript (no framework) |
| Auth | Firebase Authentication (Google OAuth + email/password) |
| Database | Firebase Firestore (cloud) + localStorage (local fallback) |
| Hosting | Firebase Hosting |
| AI | Claude API (Anthropic) with offline smart coach fallback |
| Version control | Git + GitHub |

---

## Project Structure

```
PocketWiseApp/
├── index.html        # App shell — loads CSS, Firebase SDKs, and app.js
├── styles.css        # All styling — theme variables, layout, components
├── app.js            # All JavaScript — state, auth, rendering, AI coach
├── firebase.json     # Firebase Hosting + Firestore config
├── firestore.rules   # Firestore security rules (users can only access their own data)
├── .firebaserc       # Firebase project link (pocketwise-4e244)
└── public/           # Default Firebase Hosting placeholder (not used by the app)
```

---

## How It Was Built

The app is a single-page application (SPA) built with plain HTML, CSS, and JavaScript — no frameworks or build tools. The entire UI is rendered by a single `render()` function in `app.js` that rebuilds the page whenever the app state changes.

### State management
All data lives in a single `state` object. Every user action (adding a transaction, switching tabs, logging in) updates the state and calls `render()`, which rebuilds the relevant part of the UI.

### Authentication
- **Google Sign-In** uses Firebase Authentication's `signInWithPopup` with `GoogleAuthProvider`. The `onAuthStateChanged` listener restores the session automatically on page load.
- **Email/password** accounts are created and verified locally using `localStorage`. Passwords are validated for strength (min 8 chars, uppercase, lowercase, number) before being accepted.
- The **demo account** is a pre-loaded dataset that lets anyone try the app without signing up.

### Data storage
- Google OAuth users write to **Firestore** under `users/{uid}`. Transactions and goals are stored as arrays in a single document per user.
- Email/password and demo users write to **localStorage** keyed by email.
- Firestore security rules ensure each user can only read and write their own document.

### AI Coach
The coach first attempts to call the **Claude API** (`claude-sonnet-4-20250514`) with a system prompt that includes the user's financial snapshot and recent transactions. If the API is unavailable or times out (8 seconds), it falls back to `smartCoachReply()` — a pattern-matching function that generates personalised responses from the user's actual data entirely offline.

### Theming
CSS custom properties (variables) on `:root[data-theme]` control every colour in the app. Switching theme just toggles the `data-theme` attribute on `<html>` and saves the preference to localStorage.

---

## Running Locally

The app requires Firebase Hosting to serve the `/__/firebase/` SDK scripts. Use the Firebase local emulator instead of opening `index.html` directly:

```bash
npm install -g firebase-tools
firebase login
firebase serve
```

Then open `http://localhost:5000` in your browser.

---

## Deploying

```bash
firebase deploy
```

This deploys both the app to Firebase Hosting and the Firestore security rules.

Live URL: **https://pocketwise-4e244.web.app**

---

## Future Ideas

- Push notifications for budget limits
- Monthly spending reports sent by email
- Multi-currency support
- Export transactions to CSV
