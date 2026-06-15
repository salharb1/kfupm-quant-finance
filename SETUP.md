# Enable Google login + cross-device sync (one-time, ~5 min, free)

The site works without this. Doing it lets you sign in with Google so your
progress is saved to your account and follows you across phone and desktop.

It uses **Firebase** (Google's own service). Free tier is far more than enough.

## 1. Create a Firebase project
1. Go to https://console.firebase.google.com → **Add project**.
2. Name it (e.g. `kfupm-quant`). You can disable Google Analytics. Click create.

## 2. Add a Web app and copy the config
1. In the project, click the **`</>` (Web)** icon to "Add app to get started".
2. Give it a nickname, **don't** check Hosting, click **Register app**.
3. You'll see a `firebaseConfig = { apiKey: "...", ... }` object. **Copy those 6 values.**

## 3. Turn on Google sign-in
1. Left menu → **Build → Authentication → Get started**.
2. **Sign-in method** tab → **Google** → enable → pick a support email → **Save**.
3. Still in Authentication → **Settings → Authorized domains → Add domain**, and add:
   - `salharb1.github.io`
   - `localhost` (usually already there)

## 4. Create the database
1. Left menu → **Build → Firestore Database → Create database**.
2. Choose **Production mode**, pick a location, **Enable**.
3. Open the **Rules** tab, replace everything with the rules below, **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```
(Each person can only read/write their own progress.)

## 5. Paste the config into the site
Open `index.html`, find the block near the bottom:

```js
const FIREBASE_CONFIG = {
  apiKey: "PASTE_API_KEY",
  ...
};
```
Replace the `PASTE_...` values with the 6 values from step 2, save, then push:

```
git add index.html && git commit -m "Enable Google login" && git push
```

GitHub Pages rebuilds in ~1 minute. Reload the site → **Sign in with Google** now works,
and your checkboxes/progress sync to your account.

---
**Don't want to do this yourself?** Just send me the 6 config values from step 2
(they're safe to share — the Firebase web apiKey is not a secret) and I'll paste them
in and push for you. You'll still need to do steps 3–4 in the console, since those
require your Google login.
