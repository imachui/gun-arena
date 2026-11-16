# Gun Arena

3D top-down arena shooter. Practice vs bots (no sign-in) or online rooms (Google sign-in, Firebase).

## Files
- `index.html`: the whole game (put it at the repo root)
- `firestore.rules`: paste into Firebase Console > Firestore > Rules
- `database.rules.json`: paste into Firebase Console > Realtime Database > Rules

## Setup (once)
1. Firebase Console: create a project, add a Web app, copy `firebaseConfig` into `index.html`.
2. Authentication > Sign-in method > enable Google.
3. Create Firestore and Realtime Database (Singapore).
4. Publish both rules files. In `firestore.rules`, replace `PASTE_ADMIN_UID_HERE` with your admin UID.
5. In `index.html`, set `ADMIN_UIDS`, `GCASH_NUMBER`, and review `TIERS`.
6. Authentication > Settings > Authorized domains > add `YOURNAME.github.io`.

## Deploy on GitHub Pages
Repo > Settings > Pages > Source: Deploy from a branch > `main` / `(root)` > Save.
Your game: `https://YOURNAME.github.io/REPO/`

## Security note
Prize payouts and match results are decided in the players' browsers. Fine for friends and testing.
Before real money, move payouts and room settlement to a Cloud Function.
