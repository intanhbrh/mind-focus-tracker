# 🧠 FocusFlow — Daily Mind & Focus Tracker

> **A PWA web app that helps you track energy, mood, focus sessions, sleep, and get AI-powered coaching — built as a single HTML file with Firebase Google Auth.**

![FocusFlow Banner](https://img.shields.io/badge/FocusFlow-v2.0-6d5dfc?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0id2hpdGUiIGQ9Ik0xMiAyQzYuNDggMiAyIDYuNDggMiAxMnM0LjQ4IDEwIDEwIDEwIDEwLTQuNDggMTAtMTBTMTcuNTIgMiAxMiAyem0wIDE4Yy00LjQxIDAtOC0zLjU5LTgtOHMzLjU5LTggOC04IDggMy41OSA4IDgtMy41OSA4LTggOHptLjUtMTNoLTF2NmwzLjI1IDEuOTUuNzUtMS4yM0wxMi41IDE0LjV6Ii8+PC9zdmc+)
![HTML](https://img.shields.io/badge/HTML-Single%20File-orange?style=for-the-badge)
![Firebase](https://img.shields.io/badge/Firebase-Auth%20%2B%20Firestore-FFCA28?style=for-the-badge&logo=firebase)
![Claude AI](https://img.shields.io/badge/Claude%20AI-Powered-6d5dfc?style=for-the-badge)
![PWA](https://img.shields.io/badge/PWA-Installable-34d399?style=for-the-badge)

---

## 📸 Features

| Feature | Description |
|---|---|
| 🔐 Google Sign-In | Firebase Auth — one tap, data synced to your account |
| ⚡ Daily Check-In | Log mood (5 options), energy (1–10 slider), sleep, blockers, wins |
| 🎯 Focus Timer | Pomodoro-style timer — 25/45/60/90 min + custom, with animated ring |
| 📊 Insights | 7-day charts for energy, mood, focus, sleep + 28-day heatmap |
| 🤖 AI Coach | Claude-powered coach that reads your *actual* data and gives personalised advice |
| 🏆 XP & Levels | Earn XP for check-ins, focus sessions, wins — level up through 10 tiers |
| 🔥 Streaks | Daily check-in streak counter with visual motivation |
| 💧 Water Tracker | 8-cup daily tracker with animated fill |
| 😴 Sleep Logger | Quick sleep hours logging with progress bar |
| 🏅 Weekly Challenges | 5 rotating challenges per week — complete them for bonus XP |
| 💬 Daily Quotes | Rotating motivational quotes keyed to the day |
| 📤 Data Export | Download all your data as JSON |
| ✨ Particle UI | Animated background particles + floating orbs |
| 📱 PWA | Add to Home Screen on iOS/Android — works like a native app |

---

## 🚀 Live Demo

👉 **[Deploy to GitHub Pages](#-deploy-to-github-pages)** — takes 3 minutes

---

## 🛠️ Tech Stack

- **Frontend** — Pure HTML/CSS/JS (no framework, no build step)
- **Auth** — Firebase Authentication (Google Sign-In)
- **Database** — Firebase Firestore (real-time sync per user)
- **AI** — Anthropic Claude API (`claude-sonnet-4-20250514`)
- **Charts** — Chart.js 4.4
- **Fonts** — Google Fonts (Syne + DM Sans)
- **Hosting** — GitHub Pages (or Firebase Hosting)

---

## ⚙️ Setup Guide

### Step 1 — Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/focusflow.git
cd focusflow
```

### Step 2 — Create a Firebase project

1. Go to **[https://console.firebase.google.com](https://console.firebase.google.com)**
2. Click **"Add project"** → give it a name (e.g. `focusflow-app`)
3. Disable Google Analytics (optional) → **Create project**

### Step 3 — Enable Google Authentication

1. In the Firebase console, go to **Authentication → Sign-in method**
2. Click **Google** → Enable → Save
3. Add your domain to **Authorized domains**:
   - `localhost`
   - `YOUR_USERNAME.github.io`

### Step 4 — Enable Firestore Database

1. Go to **Firestore Database → Create database**
2. Choose **Start in test mode** (for development)
3. Select a region → Done

> ⚠️ For production, update Firestore **Security Rules**:
> ```
> rules_version = '2';
> service cloud.firestore {
>   match /databases/{database}/documents {
>     match /users/{userId} {
>       allow read, write: if request.auth != null && request.auth.uid == userId;
>     }
>   }
> }
> ```

### Step 5 — Get your Firebase config

1. Go to **Project Settings → General → Your apps**
2. Click **"</> Web"** → Register app
3. Copy the `firebaseConfig` object

### Step 6 — Add your config to `index.html`

Open `index.html` and find this block near the top (around line 15):

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

Replace each value with your actual Firebase config.

### Step 7 — Add your Anthropic API key

The AI Coach calls the **Anthropic Claude API** directly from the browser.

> **For development/demo only** — for production, route this through a backend.

In `index.html`, the fetch call in `sendChat()` hits `https://api.anthropic.com/v1/messages`. You need to handle CORS via a proxy or set up a simple backend function.

**Option A (quick demo) — Use a CORS proxy:**

Replace the fetch URL with your proxy:
```javascript
const r = await fetch('https://YOUR_PROXY/v1/messages', { ... });
```

**Option B — Firebase Cloud Function (recommended):**

```javascript
// functions/index.js
const functions = require('firebase-functions');
const fetch = require('node-fetch');

exports.aiCoach = functions.https.onCall(async (data, context) => {
  if (!context.auth) throw new functions.https.HttpsError('unauthenticated', 'Login required');
  const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': functions.config().anthropic.key,
      'anthropic-version': '2023-06-01'
    },
    body: JSON.stringify(data)
  });
  return response.json();
});
```

Set your key: `firebase functions:config:set anthropic.key="sk-ant-..."`

---

## 🌐 Deploy to GitHub Pages

### Option A — GitHub Pages (free, easiest)

1. Push your code to GitHub:
```bash
git init
git add .
git commit -m "Initial commit — FocusFlow v2.0"
git remote add origin https://github.com/YOUR_USERNAME/focusflow.git
git push -u origin main
```

2. Go to your repo → **Settings → Pages**
3. Under **Source**, select `main` branch → `/ (root)` → **Save**
4. Your app is live at: `https://YOUR_USERNAME.github.io/focusflow`

5. Add `https://YOUR_USERNAME.github.io` to Firebase Authorized Domains:
   - Firebase Console → Authentication → Sign-in method → Authorized domains → Add

### Option B — Firebase Hosting (better performance)

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
# Set public directory to: . (current folder)
# Configure as single-page app: Yes
firebase deploy
```

---

## 📁 Project Structure

```
focusflow/
├── index.html          # Entire app — HTML + CSS + JS in one file
├── README.md           # This file
├── FIREBASE_SETUP.md   # Detailed Firebase walkthrough
└── .gitignore          # Ignore node_modules etc
```

---

## 🎮 XP & Level System

| Level | Name | XP Required |
|---|---|---|
| 1 | Beginner | 0 |
| 2 | Consistent | 100 |
| 3 | Focused | 300 |
| 4 | Disciplined | 600 |
| 5 | Sharp | 1,000 |
| 6 | Relentless | 1,500 |
| 7 | Elite | 2,200 |
| 8 | Mind Master | 3,000 |
| 9 | Flow State | 4,000 |
| 10 | Legend | 5,500 |

**How to earn XP:**
- ✅ Daily check-in → **+20 XP**
- 🎯 Focus session → **+5 XP per 5 minutes**
- 🏆 Win logged → **+5 XP each**
- 💧 8 cups water → **+10 XP**
- 🏅 Weekly challenge → **+20–40 XP**
- 🎉 First sign-up → **+50 XP**

---

## 📱 Install as PWA

### iPhone / iPad (Safari)
1. Open the app in Safari
2. Tap the **Share** button (box with arrow)
3. Tap **"Add to Home Screen"**
4. Tap **Add** — done!

### Android (Chrome)
1. Open the app in Chrome
2. Tap the **⋮** menu → **"Add to Home screen"**
3. Tap **Add**

---

## 🔒 Privacy & Data

- User data is stored in **Firestore under their own UID** — no one else can read it
- No analytics, no ads, no tracking
- All data can be exported as JSON from the Profile screen
- Demo mode stores data in **localStorage only** (no server)

---

## 🤝 Contributing

Pull requests welcome! Ideas for contributions:

- [ ] Push notifications for daily reminders
- [ ] Dark/light theme toggle
- [ ] Habit tracker (custom habits)
- [ ] Export to CSV
- [ ] Shareable streak cards
- [ ] More chart types in Insights
- [ ] Offline support (Service Worker)

---

## 👨‍💻 Built By

Made with ☕ and focus by a software engineering student.  
Built as a portfolio project — feel free to fork and make it your own.

---

## 📄 License

MIT License — free to use, modify, and distribute.

```
MIT License

Copyright (c) 2025 FocusFlow

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so.
```

---

<div align="center">
  <strong>🧠 Track your mind. Build your best days.</strong><br>
  <sub>FocusFlow v2.0 · Powered by Claude AI · Built for curious minds</sub>
</div>
