# My Note Pad — GitHub Pages PWA

## Features
- Bengali / English interface, dark mode
- Create, edit, search, pin, favorite, soft-delete notes
- Automatic local saving with IndexedDB
- JSON backup and restore
- Optional PIN lock
- Installable PWA with service worker
- Optional Firebase email/password login and Firestore sync

## A. Publish on GitHub Pages (Android or desktop)
1. Create/sign in to GitHub.
2. Create a **public** repository, e.g. `my-note-pad`.
3. Upload all files and the `icons` folder from this project to the repository root. Do not upload only the ZIP.
4. Open repository **Settings → Pages**.
5. Under Build and deployment, choose **Deploy from a branch**, branch `main`, folder `/(root)`, then Save.
6. Wait for GitHub to publish the site. Open the URL shown in Pages over HTTPS.
7. In Chrome on Android, open the URL → menu (⋮) → **Install app** or **Add to Home screen**.

## B. Offline use
- Notes are saved in IndexedDB on the current browser/device.
- Open the app online once so the service worker can cache its app files.
- After that, basic note features work offline.
- Do not clear Chrome site data unless you have a backup. Browser-local notes do not automatically move to another phone.

## C. Enable Firebase online sync
1. Go to https://console.firebase.google.com/ and create a Firebase project.
2. Add a Web App and copy its config values.
3. In **Authentication → Sign-in method**, enable **Email/Password**.
4. In **Firestore Database**, create a database.
5. Edit `firebase-config.js`, replacing all `PASTE_...` values with your project's `apiKey`, `authDomain`, `projectId`, and `appId`.
6. In Firestore Rules, use these rules and publish them:

```text
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/notes/{noteId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

7. Commit/push the updated file to GitHub. Wait for Pages to redeploy.
8. Open the app, Settings → Online Sync. Create an account or sign in using the same email on each device. Press Sync Now if needed.

Firebase Web config is not an admin secret, but Firestore Security Rules and Authentication are essential. Never put a Firebase service-account key or Admin SDK credentials in this website.

## D. PIN and privacy notes
- PIN is an app-level lock stored in the browser. It is not strong encryption and cannot protect against someone who can inspect or modify browser storage.
- If you forget the PIN, this starter app has no PIN recovery mechanism. Keep a backup before setting a PIN.
- Use the phone's screen lock for sensitive data.

## E. Backup and restore
- Open Settings → Backup & Restore → Download Backup.
- Restore by selecting a JSON backup file.
- Keep backup files in a safe place. Restore adds/updates notes by ID; it does not delete unrelated notes.

## Notes about sync behavior
- This starter project uses a simple last-updated timestamp merge. If two devices edit the same note while offline, the latest timestamp wins and may overwrite the other edit. Export backups regularly.
- Trash is a soft-delete and syncs as a deleted note. Permanent deletion is not included, to avoid accidental cross-device data loss.
