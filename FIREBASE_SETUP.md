# 🔥 Firebase Setup — Step by Step

This guide walks you through setting up Firebase for FocusFlow from scratch.

---

## 1. Create a Firebase Project

1. Go to [https://console.firebase.google.com](https://console.firebase.google.com)
2. Click **"Add project"**
3. Name it `focusflow` (or anything you like)
4. Disable Google Analytics → **Create project**
5. Wait ~30 seconds for setup to complete

---

## 2. Register a Web App

1. On the project overview page, click the **`</>`** (Web) icon
2. App nickname: `FocusFlow Web`
3. ✅ Check **"Also set up Firebase Hosting"** (optional)
4. Click **Register app**
5. **Copy the `firebaseConfig` object** — you'll need it in step 6

It looks like this:
```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "focusflow-xxxxx.firebaseapp.com",
  projectId: "focusflow-xxxxx",
  storageBucket: "focusflow-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

---

## 3. Enable Google Sign-In

1. Left sidebar → **Authentication** → **Get started**
2. Click **Sign-in method** tab
3. Click **Google** → Toggle **Enable** → **Save**
4. Go to **Settings** tab → **Authorized domains**
5. Add these domains:
   - `localhost`
   - `YOUR_USERNAME.github.io`
   - Your custom domain (if any)

---

## 4. Create Firestore Database

1. Left sidebar → **Firestore Database** → **Create database**
2. Select **Start in test mode** → **Next**
3. Choose a region closest to you → **Done**

### Production Security Rules

Once you're ready to go live, update the rules:

1. Firestore → **Rules** tab
2. Replace the content with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null 
                         && request.auth.uid == userId;
    }
  }
}
```

3. Click **Publish**

---

## 5. Paste Config into index.html

Open `index.html` and find this section at the top (inside the `<script type="module">`):

```javascript
// ── PASTE YOUR FIREBASE CONFIG HERE ──
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

Replace each placeholder with your real values from Step 2.

---

## 6. Test Locally

Open `index.html` directly in Chrome, or use VS Code Live Server:

```bash
# Install Live Server globally
npm install -g live-server

# Run from project folder
live-server
```

Visit `http://localhost:8080` — click **Continue with Google** and test the auth flow.

---

## 7. Deploy

### GitHub Pages
```bash
git add .
git commit -m "Add Firebase config"
git push origin main
```
Then enable GitHub Pages in repo Settings → Pages → Source: `main / root`.

### Firebase Hosting
```bash
npm install -g firebase-tools
firebase login
firebase init hosting
# Public directory: . (dot, current folder)
# Single page app: Yes
# Overwrite index.html: No
firebase deploy
```

---

## Common Issues

| Problem | Fix |
|---|---|
| `auth/unauthorized-domain` | Add your domain to Firebase → Auth → Authorized domains |
| Popup blocked | Allow popups for your domain in browser settings |
| Firestore permission denied | Check security rules — make sure test mode is active or rules are correct |
| Config not working | Double-check all 6 values are pasted correctly, no trailing spaces |
